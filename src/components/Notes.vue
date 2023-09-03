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
                <button class="noteRemove material-symbols-outlined" @click="removeNote(note)">delete</button>
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
    color: white;
}

.Notes div{
    background-color: rgba(255, 255, 255, 0.15);
    padding: 1.25em;
    border-radius: 20px;
    margin: 0.9375em 1.25em;
}

ul{
    background-color: rgb(84, 87, 92);
    padding: 0.625em;
    margin: 0 1.25em;
    border-radius: 20px;
    padding: 1.25em;
}

/* content */

    /* note top */

.Notes div > h1{
    margin-top: 0;
}

.addInput{
    padding: 0.625em;
    border-radius: 10px;
    color: black;
    background: white;
    border: black 2px solid;
}

.addInput::placeholder{
    color: black;
    opacity: 1;
}

.addButton{
    padding: 0.625em;
    border-radius: 10px;
    margin-left: 0.3125em;
    background: white;
    border: black 2px solid;
    font-family: "Montserrat", sans-serif;
}

.addButton:hover{
    background: rgb(199, 199, 199);
}

    /* note list */

.noteRemove{
    background: crimson;
    border: black 2px solid;
    border-radius: 15px;
    padding: 0.3125em;
    text-align: center;
}

.noteRemove:hover{
    background: rgb(164, 35, 61);
}

.noteText{
    font-size: 1.4375em;
    padding: 0.625em;
}

ul > li{
    list-style: none;
}

ul > li > *{
    display: inline-block;
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
