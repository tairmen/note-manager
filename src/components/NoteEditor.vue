<template>
  <div class="note-editor">
    <button
      type="button"
      @click="saveNote"
      class="note-button"
    >Save</button>
    <button
      type="button"
      @click="lastChange"
      class="note-button"
    >&#8592;</button>
    <button
      type="button"
      @click="returnChange"
      class="note-button"
    >&#8594;</button>
    <button
      type="button"
      @click="showConfirmDelete = true"
      class="note-button"
    >Delete</button>
    <button
      type="button"
      @click="showConfirmClose = true"
      class="note-button"
    >Close</button>
    <div class="editor-body">
      <div class="title-container">
        <input type="text" class="editor-title" v-model="editableNote.title" />
      </div>
      <button type="button" @click="addTodo" class="todo-add-button">Add</button>
      <div class="todo-list">
        <div class="todo-item" v-for="(item, idx) in editableNote.list" :key="idx">
          <div class="todo-checkbox">
            <input type="checkbox" @change="changedHandler" v-model="item.status" />
          </div>
          <textarea class="todo-text" @change="changedHandler" v-model="item.name" />
          <button @click="deleteTodo(idx)" class="todo-delete-button" type="button">
            <svg
              class="bi bi-trash"
              width="1em"
              height="1em"
              viewBox="0 0 16 16"
              fill="currentColor"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M5.5 5.5A.5.5 0 016 6v6a.5.5 0 01-1 0V6a.5.5 0 01.5-.5zm2.5 0a.5.5 0 01.5.5v6a.5.5 0 01-1 0V6a.5.5 0 01.5-.5zm3 .5a.5.5 0 00-1 0v6a.5.5 0 001 0V6z"
              />
              <path
                fill-rule="evenodd"
                d="M14.5 3a1 1 0 01-1 1H13v9a2 2 0 01-2 2H5a2 2 0 01-2-2V4h-.5a1 1 0 01-1-1V2a1 1 0 011-1H6a1 1 0 011-1h2a1 1 0 011 1h3.5a1 1 0 011 1v1zM4.118 4L4 4.059V13a1 1 0 001 1h6a1 1 0 001-1V4.059L11.882 4H4.118zM2.5 3V2h11v1h-11z"
                clip-rule="evenodd"
              />
            </svg>
          </button>
        </div>
      </div>
    </div>
    <!-- компонент confirm для подтверждения удаления -->
    <vue-confirm
      v-if="showConfirmDelete" 
      @close="showConfirmDelete = false" 
      @confirm="deleteNote"
      title="Confirm" 
      msg="You are sure that you want to delete this note?" 
    ></vue-confirm>
    <!-- компонент confirm для подтверждения вьхода из режима редактирования -->
    <vue-confirm
      v-if="showConfirmClose" 
      @close="showConfirmClose = false" 
      @confirm="closeNote"
      title="Close" 
      msg="All changes will be lost. Do you want to close?" 
    ></vue-confirm>
  </div>
</template>

<script>
import vueConfirm from './comfirm.vue'
export default {
  name: "NoteEditor",
  props: {
    note: null
  },
  data() {
    let me = this;
    return {
      showConfirmDelete: false,
      showConfirmClose: false,
      editableNote: JSON.parse(JSON.stringify(me.note)), // создаем копию заметки для редактирования
      changesStorage: [], // масив состояний заметки
      currentChangeIndex: 0, // указатель на елемент масива состояний на даньй момент
    };
  },
  components: {
    'vue-confirm': vueConfirm
  },
  methods: {
    closeNote() {
      let me = this;
      me.$emit("close");
    },
    saveNote() {
      let me = this;
      // при сохранинии передаем editableNote
      me.$emit("save", me.editableNote);
    },
    deleteNote() {
      let me = this;
      me.showConfirmDelete = false;
      me.emit("delete-note", me.editableNote);
    },
    lastChange() {
      let me = this;
      // console.log(me.currentChangeIndex, me.changesStorage)
      // переходим на предьдущее состояние из changesStorage
      if (me.currentChangeIndex > 0 && me.changesStorage.length > 0) {
        if (me.currentChangeIndex == 1) {
          // если предьдущего состояния нет в changesStorage, то переходим к исходному состоянию
          me.editableNote = JSON.parse(JSON.stringify(me.note));
        } else {
          me.editableNote = JSON.parse(me.changesStorage[me.currentChangeIndex - 2]);
        }      
        me.currentChangeIndex -= 1;
      } else {
        console.error('no changes');
      }
    },
    returnChange() {
      let me = this;
      // возвращение отмененного состояния
      if (me.currentChangeIndex < me.changesStorage.length && me.changesStorage.length > 0) {
        me.editableNote = JSON.parse(me.changesStorage[me.currentChangeIndex]);
        me.currentChangeIndex += 1;
      } else {
        console.error('no changes');
      }
    },
    addTodo() {
      let me = this;
      // при добавлении генерируем пустой елемент и добавлеям состояние
      me.editableNote.list.push({
        status: false,
        name: ""
      });
      me.addState(); 
    },
    deleteTodo(index) {
      let me = this;
      // удаляем елемент и добавлеям состояние
      me.editableNote.list.splice(index, 1);
      me.addState(); 
    },
    changedHandler() {
      let me = this;
      me.addState();      
    },
    addState() {
      let me = this;
      let noteState = JSON.stringify(me.editableNote);
      me.changesStorage.push(noteState);
      me.currentChangeIndex += 1;
    }
  }
};
</script>

<style scoped>
.note-editor {
  margin: auto;
  width: calc(100vw - 100px);
  max-width: 500px;
  padding: 20px;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
}
.note-button {
  margin: 5px;
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
.note-button:hover {
  background-color: #cdcfd1;
  color: white;
}
.todo-add-button {
  color: black;
  background-color: white;
  padding: 6px 12px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 8px 2px;
  cursor: pointer;
  border: 1px solid #5c5c5c;
  transition-duration: 0.3s;
}
.todo-add-button:hover {
  background-color: #5c5c5c;
  color: white;
}
.todo-list {
  margin: 2px;
  padding: 15px 10px;
  border: 1px solid gray;
  border-radius: 8px;
}
.title-container {
  text-align: center;
  margin: 15px 10px;
}
.editor-title {
  padding: 4px 10px;
  font-size: 22px;
  text-align: center;
}
.todo-item {
  display: grid;
  grid-template-columns: 30px auto 50px;
  margin: 10px 5px;
}
.todo-checkbox {
  border: 1px solid #a3a3a3;
  display: flex;
  justify-content: center;
  align-items: center;
}
.todo-text {
  resize: vertical;
}
.todo-delete-button {
  color: black;
  background-color: white;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  cursor: pointer;
  border: 1px solid #a3a3a3;
  transition-duration: 0.3s;
}
.todo-delete-button:hover {
  background-color: #5c5c5c;
  color: white;
}
</style>
