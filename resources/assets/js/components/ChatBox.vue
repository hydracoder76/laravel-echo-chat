<template>
    <div>
        <p v-for="message in messages"> {{ message }}</p>
        <input v-model="text">
        <button @click="postMessage" :disabled="!contentExists">submit</button>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                text: '',
                messages: []
            }
        },
        computed: {
            contentExists() {
                return this.text.length > 0;
            }
        },
        methods: {
            postMessage() {
                axios.post('/api/post', {message: this.text}).then(({data}) => {
                    this.messages.push(data);
                    this.text = '';
                });
            }
        },
        created() {
            axios.get('/getAll').then(({data}) => {
                this.messages = data;
            });
            Echo.channel('public').listen('MessageSent',({message})=>{
                this.messages.push(message);
            })
        }
    }
</script>