<template>
    <div class="container">
        <div class="popupBg">
            <div class="popupContent">
                <h1>Change your note:</h1>
                <input class="addInput" type="text" v-model="oldNote.content">
                <button class="addButton" @click="$emit('popupClosed', oldNote)">Submit</button>
            </div>
        </div>
    </div>
</template>

<script>
import { ref } from 'vue';

export default{
    name: "ChangeNotePopup",
    props: ['oldNote'],
    setup(props){
        let oldNote = props.oldNote

        return{
            oldNote
        }
    }
}
</script>

<style>
.container{
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    text-align: center;
}

.popupBg{
    background: rgb(127, 129, 133);
    display: inline-block;
    border-radius: 25px;
    padding: 3.125em;
    border: black 0.25em solid;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

.popupContent{
    color: white;
}

.popupContent > h1{
    font-size: 4em;
    margin-top: 0;
}

.popupContent > input, .popupContent > button{
    font-size: 1.5em;
}

.popupContent > input{
    font-family: "Montserrat", sans-serif;
}
</style>