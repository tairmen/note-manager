<template>
  <div class="note-editor shadow">
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
      <h5 class="editor-title">{{note.title}}</h5>
      <button type="button" @click="addTodo" class="todo-add-button">Add</button>
      <div class="todo-list">
        <div class="todo-item" v-for="(item, idx) in note.list" :key="idx">
          <div class="todo-checkbox">
            <input type="checkbox" @change="checkboxChanged(idx)" v-model="item.status" />
          </div>
          <textarea class="todo-text" @change="textChanged(idx)" v-model="item.name" />
          <button @click="deleteTodo" class="todo-delete-button" type="button">
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
    <vue-confirm
      v-if="showConfirmDelete" 
      @close="showConfirmDelete = false" 
      @confirm="deleteNote"
      title="Confirm" 
      msg="You are sure that you want to delete selected items?" 
    ></vue-confirm>
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
      initialState: JSON.parse(JSON.stringify(me.note)),
      changesStorage: [],
      currentChangeIndex: -1,
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
      me.$emit("save");
    },
    deleteNote() {
      let me = this;
      me.emit("delete-note");
    },
    lastChange() {
      // let me = this;
      // if (me.currentChangeIndex > -1) {

      // }
    },
    returnChange() {

    },
    addTodo() {

    },
    deleteTodo() {

    },
    checkboxChanged(index) {
      let me = this;
      me.changesStorage.push({
        index: index,
        checkbox: me.note.status
      });
      me.currentChangeIndex += 1;
    },
    textChanged(index) {
      let me = this;
      me.changesStorage.push({
        index: index,
        text: me.note.text
      });
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
  transition-duration: 0.4s;
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
.editor-title {
  margin: 15px 10px;
  text-align: center;
  font-size: 22px;
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