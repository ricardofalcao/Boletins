<template>
    <div class="person bg-gray-400 m-1 relative">
        <div class="py-4 px-8 cursor-pointer select-none" @click.prevent="$emit('increment')" @contextmenu.prevent="$emit('decrement')" >
            <span>{{ name }}</span>: <span>{{ score }}</span>
        </div>

        <!--<span  @click="$emit('destroy')" class="stroke-current text-red-800 absolute top-0 right-0 cursor-pointer">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24"
             viewBox="0 0 24 24" stroke-width="1" fill="none" stroke-linecap="round"
             stroke-linejoin="round">
            <path stroke="none" d="M0 0h24v24H0z"/>
            <line x1="4" y1="7" x2="20" y2="7"/>
            <line x1="10" y1="11" x2="10" y2="17"/>
            <line x1="14" y1="11" x2="14" y2="17"/>
            <path d="M5 7l1 12a2 2 0 0 0 2 2h8a2 2 0 0 0 2 -2l1 -12"/>
            <path d="M9 7v-3a1 1 0 0 1 1 -1h4a1 1 0 0 1 1 1v3"/>
        </svg>
    </span>-->
    </div>
</template>

<script>
    export default {
        name: 'Person',
        props: {
            name: String,
            score: Number,
        },
        methods: {
            onNameClick() {
                this.votes++;
            }
        }
    }
</script>
