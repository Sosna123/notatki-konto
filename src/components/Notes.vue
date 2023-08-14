<template>
    <h1>Notes:</h1>
    <input type="text" v-model="currentNote">
    <button @click="addNote">Submit</button>
    <div>
        <div v-for="note in notes" :key="note">
            <p>{{ note }}</p>
            <button @click="removeNote(note)">remove</button>
        </div>
    </div>
</template>

<script>
import { ref } from 'vue';

export default {
    name: 'Home',
    setup(){
        let currentNote = ref('');
        let notes = ref([]);

        let addNote = () => {
            notes.value.push(currentNote.value)
            currentNote.value = ''
        }

        let removeNote = (note) => {
            notes.value = notes.value.filter((el) => {
                if(el == note){
                    return false;
                }else{
                    return true;
                }
            })
        }

        return{
            currentNote, notes,
            addNote, removeNote
        }
    }
}
</script>

<style>

</style>
