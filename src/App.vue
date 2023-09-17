<template>
    <router-view/>
</template>

<script>
export default{
    
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400&display=swap');

body{
    padding: 0;
    margin: 0;
    font-family: "Montserrat", sans-serif;
    background: #36393f;
}

@media screen and (max-width: 700px){
    body{
        font-size: 10px;
    }

    button.noteButton{
        width: 1.825em;
        height: 1.825em;
    }
}
</style>
