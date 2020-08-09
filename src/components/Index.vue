<template>
    <div class="index">
        <div class="flex flex-wrap items-center p-4">
            <div class="w-full md:w-1/2 lg:w-1/4 mx-4">
                <form @submit.prevent="addPerson">
                    <div class="flex items-center border-b border-teal-500 py-2">
                        <input v-model="inputName"
                               class="appearance-none bg-transparent border-none w-full text-gray-700 mr-3 py-1 px-2 leading-tight focus:outline-none"
                               type="text" placeholder="Nome">
                        <input class="bg-teal-500 hover:bg-teal-700 border-teal-500 hover:border-teal-700 text-sm border-4 text-white py-1 px-2 rounded"
                               type="submit" value="Adicionar">
                    </div>
                </form>
            </div>

            <div class="w-full md:w-1/2 lg:w-1/4 mx-4">
                <form @submit.prevent="importPersons">
                    <div class="flex items-center border-b border-teal-500 py-2">
                        <input ref="inputFiles" accept=".csv"
                               class="appearance-none bg-transparent border-none w-full text-gray-700 mr-3 leading-tight focus:outline-none"
                               type="file">
                        <input class="bg-teal-500 hover:bg-teal-700 border-teal-500 hover:border-teal-700 text-sm border-4 text-white py-1 px-2 rounded"
                               type="submit" value="Importar">
                    </div>
                </form>
            </div>

            <div class="mx-4 py-2">
                <button @click="exportAll" class="bg-teal-500 hover:bg-teal-700 border-teal-500 hover:border-teal-700 text-sm border-4 text-white py-1 px-2 rounded">
                    Exportar tudo
                </button>
            </div>

            <div class="mx-4 py-2">
                <button @click="destroyAll" class="bg-red-500 hover:bg-red-700 border-red-500 hover:border-red-700 text-sm border-4 text-white py-1 px-2 rounded">
                    Limpar tudo
                </button>
            </div>

            <div class="mx-4 py-2">
                <span class="text-sm">
                    Soma: {{ scoreSum }}
                </span>
            </div>
        </div>

        <div class="flex flex-wrap p-4">
            <Person v-on:destroy="destroyPerson(person)" v-on:increment="incrementScore(person, 1)" v-on:decrement="incrementScore(person, -1)" v-for="(person, index) in sortedPersons" v-bind:key="person.name + '-' + index" v-bind:name="person.name" v-bind:score="person.score"></Person>
        </div>
    </div>
</template>

<script>
    import Person from '@/components/Person.vue'

    export default {
        name: 'HelloWorld',
        components: {
            Person
        },
        data() {
            return {
                inputName: '',
                persons: []
            }
        },
        computed: {
            sortedPersons() {
                return this.persons.slice().sort((a, b) => a.name > b.name ? 1 : -1)
            },
            scoreSum() {
                var total = 0;

                for (var i = 0; i < this.persons.length; i++) {
                    total += this.persons[i].score;
                }

                return total
            }
        },
        props: {
            msg: String
        },
        mounted() {
            if (localStorage.persons) {
                this.persons = JSON.parse(localStorage.persons);
            }
        },
        watch: {
            persons(newPersons) {
                localStorage.persons = JSON.stringify(newPersons)
            }
        },
        methods: {
            destroyPerson(person) {
                this.persons.splice(this.persons.indexOf(person), 1)
            },
            destroyAll() {
                this.persons = []
            },
            exportAll() {
                var builder = "";

                for (var i = 0; i < this.persons.length; i++) {
                    var person = this.persons[i];

                    builder += person.name + ',' + person.score + '\r\n'
                }

                var element = document.createElement('a');
                element.setAttribute('href', 'data:text/csv;charset=utf-8,' + encodeURIComponent(builder));
                element.setAttribute('download', "export.csv");

                element.style.display = 'none';
                document.body.appendChild(element);

                element.click();

                document.body.removeChild(element);
            },
            incrementScore(person, amount) {
                person.score += amount;
                person.score = Math.max(person.score, 0)

                localStorage.persons = JSON.stringify(this.persons)
            },
            addPerson() {
                if (this.inputName.length > 0) {
                    this.persons.push({
                        name: this.inputName,
                        score: 0
                    })
                }
            },
            importPersons() {
                var files = this.$refs.inputFiles.files
                if (files.length == 0) {
                    return;
                }

                const reader = new FileReader();

                reader.addEventListener('load', (event) => {
                    var jsonObject = event.target.result.replace("\"", "").split(/\r?\n|\r/);

                    for (var i = 0; i < jsonObject.length; i++) {
                        var array = jsonObject[i].split(',')
                        var name = array[0]
                        if(name.length == 0) {
                            continue
                        }

                        var score = 0;
                        if (array.length > 1) {
                            score = parseInt(array[1])
                        }

                        if (isNaN(score)) {
                            score = 0
                        }

                        this.persons.push({
                            name: name,
                            score: score
                        })
                    }
                });

                reader.readAsText(files[0]);
            }
        }
    }
</script>
