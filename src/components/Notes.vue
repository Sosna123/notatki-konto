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
                    <button class="noteCheckbox noteButton material-symbols-outlined" @click="editNoteComplete(note)">
                    <span v-if="!note.isComplete" class="material-symbols-outlined">check_box_outline_blank</span>
                    <span v-else class="material-symbols-outlined">select_check_box</span>
                </button>
                <button class="noteButton material-symbols-outlined" @click="editNoteBefore(note)">edit</button>
                <button class="noteButton material-symbols-outlined" @click="removeNote(note)">delete</button>
                <p :class="{noteText: true, completeNote: note.isComplete}">{{ note.content }}</p>
            </li>
        </ul>
    </div>
    <div v-if="isEditing">
        <ChangeNotePopup :oldNote="noteToEdit" @popupClosed="(newNote) => editNoteAfter(newNote)"/>
    </div>
</template>

<script>
import { onMounted, ref } from 'vue';
import ChangeNotePopup from './ChangeNotePopup.vue';

export default {
    components: {ChangeNotePopup},
    name: 'Home',
    setup(){
        let currentNote = ref('');
        let notes = ref([]);
        let noteToEdit = ref({})
        let isEditing = ref(false);
        let noteContentBeforeEdit = ''
        let apiUri = 'https://2ff7-83-242-114-90.ngrok-free.app/'

        // fetche
        let fetchData = async () => {
            const response = await fetch(apiUri, {
                method: 'GET',
                headers: {
                    'ngrok-skip-browser-warning': 1
                }
            });
            const data = response.json()
            return data;
        }

        let fetchDataPost = async (data) => {
            await fetch(apiUri, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'ngrok-skip-browser-warning': 1
                },
                body: JSON.stringify(data)
            })
        }

        let fetchDataDelete = async (id) => {
            fetch(`${apiUri}${id}`, {
                    method: 'DELETE',
                    headers: {
                        'ngrok-skip-browser-warning': 1
                    }
            })
        }

        let fetchDataPut = async (id, note) => {
            await fetch(`${apiUri}${id}`, {
                method: 'PUT',
                headers: {
                    'Content-Type': 'application/json',
                    'ngrok-skip-browser-warning': 1
                },
                body: JSON.stringify(note)
            })
        }

        // funkcje
        let addNote = () => {
            if(currentNote.value != '') {
                let pushNote = {content: currentNote.value, isComplete: false};
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

        let editNoteComplete = (note) => {
            note.isComplete = !note.isComplete
            fetchDataPut(note._id, note).then(()=>{
                notes.value = []
                displayData()
            })
        }

        let editNoteBefore = (note) => {
            noteToEdit.value = note
            noteContentBeforeEdit = note.content
            isEditing.value = true
        }
        
        let editNoteAfter = (note) => {
            console.log(note)
            isEditing.value = false

            if(note.content == ''){
                note.content = noteContentBeforeEdit;
            }

            fetchDataPut(note._id, note).then(()=>{
                notes.value = []
                displayData()
            })
        }

        let displayData = () => {
            fetchData().then((data) => {
                notes.value.push(...data)
            }).catch((err) => {
                // console.log(err)
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
            currentNote, notes, isEditing, noteToEdit,
            // funkcje
            addNote, removeNote, displayData, enterOnInput, editNoteComplete, editNoteBefore, editNoteAfter
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
    background-color: rgb(84, 87, 92);
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

    /* top */

.Notes div > h1{
    margin-top: 0;
}

.addInput{
    padding: 0.625em;
    border-radius: 10px;
    color: black;
    background: white;
    border: black 2px solid;
    font-family: "Montserrat", sans-serif;
    font-size: 1.2em;
}

.addInput::placeholder{
    font-family: "Montserrat", sans-serif;
    font-size: 1.2em;
}

.addButton{
    padding: 0.625em;
    border-radius: 10px;
    margin-left: 0.3125em;
    background: white;
    border: black 2px solid;
    font-family: "Montserrat", sans-serif;
    font-size: 1.2em;
}

.addButton:hover{
    background: rgb(199, 199, 199);
    font-size: 1.2em;
}

    /* note list */

.noteButton{
    background: crimson;
    border: black 2px solid;
    border-radius: 15px;
    padding: 0.3125em;
    text-align: center;
    width: 2.5em;
    height: 2.5em;
}

.noteButton:hover{
    background: rgb(164, 35, 61);
}

.noteButton > p{
    margin: 0;
}

.noteText{
    font-size: 2em;
    padding: 0.625em;
}

.noteText.completeNote{
    color: grey;
    text-decoration: line-through;
}

li > *{
    display: inline-block;
}

h1{
    font-size: 3em;
}

li{
    list-style: none;
    display: flex;
    align-items: center;
}

li > p{
    flex-grow: 9;
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