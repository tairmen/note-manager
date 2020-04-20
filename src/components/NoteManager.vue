<template>
  <div>
    <div v-if="!editorCardPage">
      <h1 class="note-manager-title">{{ title }}</h1>
      <div class="buttons">
        <button type="button" @click="addItem" class="crud-button">Add</button>
        <button type="button" @click="editItem" class="crud-button">Edit</button>
        <button type="button" @click="showConfirmDelete = true" class="crud-button">Delete</button>
      </div>
      <div class="notes-container">
        <div class="note-card" @click="onNoteClick(note)" @dblclick="onNoteDblClick(note)" v-for="(note, index) in notes" :key="index" :id="'note' + note.id">
          <div class="card-body">
            <h5 class="card-title">{{note.title}}</h5>
            <div class="todo-list">
              <div class="todo-item" v-for="(item, idx) in note.list" :key="idx"> <div v-if="idx < numOfItems">   <input class="todo-input" type="checkbox" disabled :checked="item.status">   <label class="todo-label">     {{item.name}}   </label> </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <note-editor 
      v-if="editorCardPage" 
      :note="selected[0]"
      @close="closeCard" 
      @save="saveCard"
      @delete-note="deleteOneByCache"
    ></note-editor>
    <vue-confirm
      v-if="showConfirmDelete" 
      @close="showConfirmDelete = false" 
      @confirm="deleteItem"
      title="Confirm" 
      msg="You are sure that you want to delete selected items?" 
    >
    </vue-confirm>
  </div>
</template>

<script>
import NoteEditor from './NoteEditor.vue'
import vueConfirm from './comfirm.vue'
export default {
  name: 'NoteManager',
  props: {
    title: String
  },
  data() {
    let me = this;
    // localStorage.setItem('notes', JSON.stringify(me.initNotes()));
    let notesStr = localStorage.getItem('notes');
    let notes = JSON.parse(notesStr);
    if (!notes) {
      notes = me.initNotes();
      localStorage.setItem('notes', JSON.stringify(notes));
    }
    let clickedObj = me.createClickedObj(notes);
    return {
      notes: notes,
      selected: [],
      numOfItems: 4,
      editorCardPage: false,
      clickedObj: clickedObj,
      noteCache: null,
      cardMode: 'edit', // or 'add'
      showConfirmDelete: false
    }
  },
  components: {
    "note-editor": NoteEditor,
    "vue-confirm": vueConfirm
  },
  created() {

  },
  methods: {
    onNoteClick(note) {
      let me = this;
      if (me.clickedObj[note.id]) {
        document.querySelector('#note' + note.id).style["background-color"] = null;
        me.removeById(me.selected, note.id);
      } else {
        document.querySelector('#note' + note.id).style["background-color"] = "#b9bec4";
        me.selected.push(note);
      }
      me.clickedObj[note.id] = !me.clickedObj[note.id];
    },
    onNoteDblClick(note) {
      let me = this;
      setTimeout(() => {
        me.selected = [note];
        me.editItem();
      }, 20);   
    },
    addItem() {
      let me = this;
      me.cardMode = 'add';
      let newItem = {
        id: me.genID(),
        title: "",
        list: []
      }      
      me.noteCache = JSON.parse(JSON.stringify(newItem));
      me.notes.push(newItem);
      me.selected = [newItem];
      me.editorCardPage = true;

    },
    editItem() {
      let me = this;
      me.cardMode = 'edit';
      if (me.selected.length > 0) {
        if (me.clickedObj[me.selected[0].id]) {
          me.clickedObj[me.selected[0].id] = false;
        }
        me.editorCardPage = true;
        me.noteCache = JSON.parse(JSON.stringify(me.selected[0]));
      }
    },
    saveCard() {
      let me = this;
      me.selected = [];
      me.editorCardPage = false;
      localStorage.setItem('notes', JSON.stringify(me.notes));
    },
    closeCard() {
      let me = this;
      let idx = me.notes.findIndex(el => {
        return el.id == me.noteCache.id;
      })
      me.selected = [];
      if (idx > -1) {
        if (me.cardMode == 'edit') {
          me.notes[idx] = me.noteCache;
        }
        if (me.cardMode == 'add') {
          me.notes.splice(idx, 1);
        }
      }
      me.editorCardPage = false;
    },
    deleteItem() {
      let me = this;
      if (me.selected.length > 0) {
        me.selected.forEach(el => {
          me.removeById(me.notes, el.id);
        });
        localStorage.setItem('notes', me.notes);
      }
    },
    deleteOneByCache() {
      let me = this;
      me.removeById(me.notes, me.noteCache.id);
      localStorage.setItem('notes', me.notes);
    },
    createClickedObj(notes) {
      let res = {};
      notes.forEach(element => {
        res[element.id] = false;
      });
      return res;
    },
    removeById(array, id) {
      let ind = array.findIndex(el => {
        return el.id == id;
      });
      if (ind > -1) {
        array.splice(ind, 1);
      }
    },
    genID () {
      return '_' + Math.random().toString(36).substr(2, 9);
    },
    initNotes() {
      let me = this;
      return [
        {
          title: "Project X",
          id: me.genID(),
          list: [
            {
              name: "drink a lot",
              status: false
            },
            {
              name: "smoke weed",
              status: false
            },
            {
              name: "flex 3 hours",
              status: false
            }
          ]
        },
        {
          title: "Work tasks",
          id: me.genID(),
          list: [
            {
              name: "code web page",
              status: false
            },
            {
              name: "test js",
              status: false
            }
          ]
        },
        {
          title: "Traveling",
          id: me.genID(),
          list: [
            {
              name: "Prague",
              status: true
            },
            {
              name: "Milano",
              status: true
            },
            {
              name: "London",
              status: false
            },
            {
              name: "Barcelona",
              status: true
            },
            {
              name: "Rome",
              status: false
            },
            {
              name: "Berlin",
              status: true
            },
            {
              name: "Paris",
              status: false
            }
          ]
        },
        {
          title: "J Cole 'Middle Child'",
          id: me.genID(),
          list: [
            {
              name: `Niggas been countin' me out I’m countin' my bullets, I'm loadin’ my clips I'm writin' down names, I'm makin' a list`,
              status: false
            },
            {
              name: `I'm all in my bag, this hard as it get I do not snort powder, I might take a sip I might hit the blunt, but I'm liable to trip`,
              status: false
            },
            {
              name: `I'm all in my bag, this hard as it get I do not snort powder, I might take a sip I might hit the blunt, but I'm liable to trip`,
              status: false
            },
            {
              name: `I'm all in my bag, this hard as it get I do not snort powder, I might take a sip I might hit the blunt, but I'm liable to trip`,
              status: false
            },
            {
              name: `I'm all in my bag, this hard as it get I do not snort powder, I might take a sip I might hit the blunt, but I'm liable to trip`,
              status: false
            },
            {
              name: `I'm all in my bag, this hard as it get I do not snort powder, I might take a sip I might hit the blunt, but I'm liable to trip`,
              status: false
            }
          ]
        },
        {
          title: "Lil Baby 'Yes Indeed'",
          id: me.genID(),
          list: [
            {
              name: `The dash, it's digi', the schedule busy My head in a hoodie, my shorty a goodie`,
              status: false
            },
            {
              name: `The dash, it's digi', the schedule busy`,
              status: false
            },
            {
              name: `The dash, it's digi', the schedule busy`,
              status: false
            },
            {
              name: `The dash, it's digi', the schedule busy`,
              status: false
            },
            {
              name: `The dash, it's digi', the schedule busy`,
              status: false
            }
          ]
        }, 
        {
          title: "King’s Dead",
          id: me.genID(),
          list: [
            {
              name: `My bitch been ready, my clique been ready My shit's been ready, my check's been ready`,
              status: false
            },
            {
              name: `I bought a '87 for the weekend (The weekend)`,
              status: false
            },
            {
              name: `And it's like that, lil' bitch MVP, I don't get no sleep, no, I don't like that, lil' bitch`,
              status: false
            },
            {
              name: `And it's like that, lil' bitch MVP, I don't get no sleep, no, I don't like that, lil' bitch`,
              status: false
            },
            {
              name: `And it's like that, lil' bitch MVP, I don't get no sleep, no, I don't like that, lil' bitch`,
              status: false
            },
          ]
        },
      ]
    }
  }
}
</script>

<style scoped>
.note-manager-title {
  text-align: center;
}
.buttons {
  margin:  10px 30px;
}
.crud-button {
  color: black;
  background-color: white; 
  padding: 8px 16px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
  border: 1px solid #cdcfd1;
  transition-duration: 0.3s;
}
.crud-button:hover {
  background-color: #cdcfd1; 
  color: white;
}
.notes-container {
  margin: 20px 10px;
  display: flex;
  flex-wrap: wrap;
}
.note-card {
  min-width: 250px;
  width: 350px;
  margin: 20px;
  height: 230px;
  cursor: pointer;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
}
.note-card:hover {
  background-color: #ebebeb;
}
.card-title {
  text-align: center;
  text-overflow: ellipsis;
  white-space: nowrap;
  font-size: 17px;
  overflow: hidden;
  margin: 25px 10px 15px 10px;
}
.todo-list {
  height: 220px;
  overflow: hidden;
}
.todo-item {
  height: 1.5em;
  margin: 10px 20px 10px 10px;
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
}
.todo-input {
  margin-right: 10px;
}
</style>
