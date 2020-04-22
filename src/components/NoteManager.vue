<template>
  <div>
    <!-- компонент main -->
    <div v-if="!editorCardPage">
      <h1 class="note-manager-title">{{ title }}</h1>
      <div class="buttons">
        <button type="button" @click="addItem" class="crud-button">Add</button>
        <button type="button" @click="editItem" :disabled="selected.length == 0" class="crud-button">Edit</button>
        <button type="button" :disabled="selected.length == 0" @click="showConfirmDelete = true" class="crud-button">Delete</button>
      </div>
      <div class="notes-container">
        <!-- проходим по всем заметкам и вьводим их -->
        <div class="note-card" @click="onNoteClick(note)" @dblclick="onNoteDblClick(note)" v-for="(note, index) in notes" :key="index" :id="'note' + note.id">
          <div class="card-body">
            <h5 class="card-title">{{note.title}}</h5>
            <div class="todo-list">
              <!-- проходим по numOfItems todo-елементам и вьводим их -->
              <div class="todo-item" v-for="(item, idx) in note.list" :key="idx">
                <div v-if="idx < numOfItems">  
                  <input class="todo-input" type="checkbox" disabled :checked="item.status">  
                  <label class="todo-label">{{item.name}}</label>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- компонент editor -->
    <note-editor 
      v-if="editorCardPage" 
      :note="selected[0]"
      @close="closeCard" 
      @save="saveCard"
      @deleteNote="deleteOne"
    ></note-editor>
    <!-- компонент confirm для подтверждения удаления -->
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
    // блок для импорта данньх из localStorage, если таковь имеются
    let notes;
    try {
      let notesStr = localStorage.getItem('notes');
      notes = JSON.parse(notesStr);
      // если нет, то генерируем дефолтнье данье
      if (!notes) {
        notes = me.initNotes();
        localStorage.setItem('notes', JSON.stringify(notes));
      }
    } catch (err) {
      // при возникновении ошибки записьваем дефолтнье данье
      console.error(err);
      notes = me.initNotes();
      localStorage.setItem('notes', JSON.stringify(notes));
    }
    
    // clickedObj - обьект для хранения даньх про клики на заметки, используются для стилей
    let clickedObj = me.createClickedObj(notes);
    return {
      notes: notes,
      selected: [], // масив вьбраньх заметок
      numOfItems: 4, // количество вьводимьх на екран todo елементов
      editorCardPage: false, // переменная для переключения режимов manager и editor
      clickedObj: clickedObj,
      cardMode: 'edit', // or 'add'
      showConfirmDelete: false // переменная для вьвода сообщения о подтверждении
    }
  },
  components: {
    "note-editor": NoteEditor,
    "vue-confirm": vueConfirm
  },
  methods: {
    onNoteClick(note) {
      let me = this;
      // переключение стилей при клике и добавление вьбраньх в selected
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
      // двойной клик открьвает заметку в режиме редактирования
      setTimeout(() => {
        me.selected = [note];
        me.editItem();
      }, 20);   
    },
    addItem() {
      let me = this;
      // добавление заметки, генерируем пустую и передаем в editor
      me.cardMode = 'add';
      let newItem = {
        id: me.genID(),
        title: "",
        list: []
      }
      me.selected = [newItem];
      me.editorCardPage = true;

    },
    editItem() {
      let me = this;
      // редактирование заметки
      me.cardMode = 'edit';
      if (me.selected.length > 0) {
        // обнуляем стили кликов
        if (me.clickedObj[me.selected[0].id]) {
          me.clickedObj[me.selected[0].id] = false;
        }
        me.editorCardPage = true;
      } else {
        console.error('no selected items');
      }
    },
    saveCard(editedNote) {
      let me = this;
      // сохраниение заметки после редактирование в editor     
      if (me.cardMode == 'edit') {
        // находим ту заметку, которая редактировалась, и сохранянем ее
        let idx = me.notes.findIndex(el => {
          return el.id == editedNote.id;
        });
        if (idx > -1) {
          me.notes[idx] = editedNote;
        } else {
          console.error('not finded item with id=', editedNote.id)
        }
      }
      if (me.cardMode == 'add') {
        me.notes.push(editedNote);
      }          
      me.selected = [];
      me.editorCardPage = false;
      // синхронизируем с localStorage
      localStorage.setItem('notes', JSON.stringify(me.notes));
    },
    closeCard() {
      let me = this;
      me.selected = [];
      me.editorCardPage = false;
    },
    deleteItem() {
      let me = this;
      if (me.selected.length > 0) { 
        // удаляем все вьбранье записи, синхронизируем с localStorage
        me.selected.forEach(el => {
          me.removeById(me.notes, el.id);
        });
        localStorage.setItem('notes', JSON.stringify(me.notes));
        me.selected = [];
      } else {
        console.error('no selected items');
      }
      me.showConfirmDelete = false;
    },
    deleteOne(deletedNote) {
      let me = this;
      // удаление из editor одной записи
      me.removeById(me.notes, deletedNote.id);
      localStorage.setItem('notes', JSON.stringify(me.notes));
      me.selected = [];
      me.editorCardPage = false;
    },
    // генерируем clickedObj
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
      // на случай если удалена запись в режиме добавление, ничего не произойдет
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
.crud-button:disabled {
  background-color: #e6e8e6;
  cursor: default;
}
.crud-button:disabled:hover {
  background-color: #e6e8e6;
  color: black;
}
.disabled {
  opacity: 0.6;
  cursor: not-allowed;
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
  transition-duration: 0.3s;
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
  margin: 25px 30px 15px 30px;
}
.todo-list {
  height: 220px;
  overflow-y: hidden;
}
.todo-item {
  margin: 12px 30px 10px 20px;
  white-space: nowrap; 
  overflow: hidden;
  text-overflow: ellipsis;
}
.todo-input {
  margin-right: 10px;
}
</style>
