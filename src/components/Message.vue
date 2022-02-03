<template>
    <div class="message-container">
        <p>{{ msg }}</p>
    </div>
</template>

<script>
    export default {
        name: "Message",
        props:{
            msg: String
        }
    }
</script>

<style scoped>
    .message-container {
        width: 100%;
        max-width: 400px;
        background-color: #CCE5FF;
        color: #004085;
        border-left: 6px solid #21c013;
        border-radius: 5px;
        margin: 25px auto;
        padding: 15px;
    }
</style>
