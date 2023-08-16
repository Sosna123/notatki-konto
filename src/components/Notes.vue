<template>
    <div class="Notes">
        <h1>Notes:</h1>
        <input type="text" v-model="currentNote">
        <button @click="addNote">Submit</button>
        <div>
            <div v-for="note in notes" :key="note">
                <p>{{ note.content }}</p>
                <button @click="removeNote(note)">remove</button>
            </div>
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

        // fetche

        let fetchData = async () => {
            const response = await fetch('http://localhost:3000')
            const data = response.json()
            return data;
        }

        let addNote = () => {
            if(currentNote.value != '') {
                notes.value.push({user: 'sosna', content: currentNote.value})
                currentNote.value = ''
            }
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

        let displayData = () => {
            fetchData().then((data) => {
                console.log(data)
                notes.value.push(...data)
            }).catch((err) => {
                console.log(err)
            })
        }

        // wczytywanie notek z api
        displayData();

        return{
            // zmienne
            currentNote, notes,
            // funkcje
            addNote, removeNote, displayData
        }
    }
}
</script>

<style>
.Notes{
    display: inline-block;
}
</style>
