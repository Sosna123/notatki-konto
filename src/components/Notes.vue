<template>
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@20..48,100..700,0..1,-50..200" />

    <div class="Notes">
        <div>
            <h1>Add a new note:</h1>
            <input @keydown="enterOnInput" type="text" v-model="currentNote" class="addInput" placeholder="Add a new note!">
            <button @click="addNote" class="addButton">Submit</button>
        </div>
        <ul id="notesList">
            <h1>Your notes:</h1>
            <li v-for="note in notes" :key="note">
                <p class="noteText">{{ note.content }}</p>
                <button @click="removeNote(note)" class="material-symbols-outlined">delete</button>
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
                let pushNote = {content: currentNote.value};
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

        let enterOnInput = (e) => {
            if(e.key === 'Enter'){
                addNote();
            }
        }

        // wczytywanie notek z api
        onMounted(() => displayData())

        return{
            // zmienne
            currentNote, notes,
            // funkcje
            addNote, removeNote, displayData, enterOnInput
        }
    }
}
</script>

<style>
/* divs */
.Notes{
    display: inline;
}

.Notes div{
    background-color: grey;
    padding: 20px;
    border-radius: 20px;
    margin: 15px 20px;
}


/* content */
.Notes div > h1{
    margin-top: 0;
}

ul > li > * {
    display: inline-block;
}

.noteText{
    font-size: 20px;
    margin-right: 10px;
    padding: 10px;
}

.addInput{
    padding: 5px;
}

.addButton{
    padding: 5px;
}

/* google icons */
.material-symbols-outlined {
  font-variation-settings:
  'FILL' 0,
  'wght' 400,
  'GRAD' 0,
  'opsz' 24
}
</style>
