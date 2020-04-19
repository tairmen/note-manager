<template>
  <div class="note-editor shadow">
    <button
      type="button"
      @click="saveNote"
      class="note-button btn btn-sm btn-outline-secondary"
    >Save</button>
    <button
      type="button"
      @click="lastChange"
      class="note-button btn btn-sm btn-outline-secondary"
    >&#8592;</button>
    <button
      type="button"
      @click="returnChange"
      class="note-button btn btn-sm btn-outline-secondary"
    >&#8594;</button>
    <button
      type="button"
      @click="deleteNote"
      class="note-button btn btn-sm btn-outline-secondary"
    >Delete</button>
    <button
      type="button"
      @click="closeNote"
      class="note-button btn btn-sm btn-outline-secondary"
    >Close</button>
    <div class="editor-body">
      <h5 class="editor-title">{{note.title}}</h5>
      <button type="button" @click="addTodo" class="note-button btn btn-sm btn-outline-dark">Add</button>
      <div class="todo-list">
        <div class="todo-item" v-for="(item, idx) in note.list" :key="idx">
          <div class="input-group mb-3">
            <div class="input-group-prepend">
              <div class="input-group-text">
                <input type="checkbox" v-model="item.status" />
              </div>
            </div>
            <textarea class="form-control" v-model="item.name" />
            <button @click="deleteTodo" class="btn btn-outline-dark" type="button">
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
    </div>
  </div>
</template>

<script>
export default {
  name: "NoteEditor",
  props: {
    note: null
  },
  data() {
    return {
      
    };
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
    deleteNote() {},
    lastChange() {},
    returnChange() {},
    addTodo() {},
    deleteTodo() {}
  }
};
</script>

<style scoped>
.note-editor {
  margin: auto;
  width: calc(100vw - 100px);
  max-width: 500px;
  padding: 20px;
}
.note-button {
  margin: 5px;
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
}
.todo-input {
  margin-right: 10px;
}
</style>
