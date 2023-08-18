<template>
    <div class="Notes">
        <h1>Notes:</h1>
        <input type="text" v-model="currentNote">
        <button @click="addNote">Submit</button>
        <ul>
            <li v-for="note in notes" :key="note">
                <p>{{ note.content }}</p>
                <button @click="removeNote(note)">remove</button>
            </li>
        </ul>
    </div>   
</template>

<script>
import { onMounted, ref } from 'vue';

export default {
    name: 'Home',
    setup(){
        let currentNote = ref('');
        let notes = ref([]);
        let apiUri = 'http://localhost:3000'

        // fetche
        let fetchData = async () => {
            const response = await fetch(apiUri)
            const data = response.json()
            return data;
        }

        let fetchDataPost = async (data) => {
            await fetch(apiUri, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(data)
            })
        }

        let fetchDataDelete = async (id) => {
            fetch(`${apiUri}/${id}`, {
                    method: 'DELETE'
            })
        }

        // funkcje
        let addNote = () => {
            if(currentNote.value != '') {
                let pushNote = {user: 'sosna', content: currentNote.value};
                fetchDataPost(pushNote).then(() => {
                    notes.value.push(pushNote);
                    currentNote.value = '';
                    pushNote = '';
                    notes.value = [];
                    displayData();
                });
            }
        }

        let removeNote = (note) => {
            notes.value = notes.value.filter((el) => {
                if(el == note){
                    fetchDataDelete(note._id);
                    return false;
                }else{
                    return true;
                }
            })
        }

        let displayData = () => {
            fetchData().then((data) => {
                notes.value.push(...data)
            }).catch((err) => {
                console.log(err)
            })
        }

        // wczytywanie notek z api
        onMounted(() => displayData())

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
