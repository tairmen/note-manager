<template>
  <div class="hello">
    <h1 class="note-manager-title">{{ title }}</h1>
    <div class="buttons">
      <button type="button" class="note-manager-crud-button btn btn-sm btn-outline-secondary">Add</button>
      <button type="button" class="note-manager-crud-button btn btn-sm btn-outline-secondary">Edit</button>
      <button type="button" class="note-manager-crud-button btn btn-sm btn-outline-secondary">Delete</button>
    </div>
    <div class="notes-container">
      <div class="note-card shadow" @click="onNoteClick(note)" @dblclick="onNoteDblClick(note)" v-for="(note, index) in notes" :key="index" :id="'note' + note.id">
        <div class="card-body">
          <h5 class="card-title">{{note.title}}</h5>
          <div class="todo-list">
            <div class="todo-item" v-for="(item, idx) in note.list" :key="idx">
              <div v-if="idx < numOfItems">
                <input class="form-check-input" type="checkbox" disabled :checked="item.status">
                <label class="form-check-label">
                  {{item.name}}
                </label>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'NoteManager',
  props: {
    title: String
  },
  data() {
    let me = this;
    return {
      notes: me.initNotes(),
      numOfItems: 4
    }
  },
  methods: {
    initNotes() {
      return [
        {
          title: "Project X",
          id: 1,
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
          id: 2,
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
          id: 3,
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
          id: 4,
          list: [
            {
              name: `Niggas been countin' me out
                I’m countin' my bullets, I'm loadin’ my clips
                I'm writin' down names, I'm makin' a list`,
              status: false
            },
            {
              name: `I'm all in my bag, this hard as it get
                I do not snort powder, I might take a sip
                I might hit the blunt, but I'm liable to trip`,
              status: false
            },
            {
              name: `I'm all in my bag, this hard as it get
                I do not snort powder, I might take a sip
                I might hit the blunt, but I'm liable to trip`,
              status: false
            },
            {
              name: `I'm all in my bag, this hard as it get
                I do not snort powder, I might take a sip
                I might hit the blunt, but I'm liable to trip`,
              status: false
            },
            {
              name: `I'm all in my bag, this hard as it get
                I do not snort powder, I might take a sip
                I might hit the blunt, but I'm liable to trip`,
              status: false
            },
            {
              name: `I'm all in my bag, this hard as it get
                I do not snort powder, I might take a sip
                I might hit the blunt, but I'm liable to trip`,
              status: false
            }
          ]
        },
        {
          title: "Lil Baby 'Yes Indeed'",
          id: 5,
          list: [
            {
              name: `The dash, it's digi', the schedule busy
                My head in a hoodie, my shorty a goodie`,
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
          id: 6,
          list: [
            {
              name: `My bitch been ready, my clique been ready
                My shit's been ready, my check's been ready`,
              status: false
            },
            {
              name: `I bought a '87 for the weekend (The weekend)`,
              status: false
            },
            {
              name: `And it's like that, lil' bitch
                MVP, I don't get no sleep, no, I don't like that, lil' bitch`,
              status: false
            },
            {
              name: `And it's like that, lil' bitch
                MVP, I don't get no sleep, no, I don't like that, lil' bitch`,
              status: false
            },
            {
              name: `And it's like that, lil' bitch
                MVP, I don't get no sleep, no, I don't like that, lil' bitch`,
              status: false
            },
          ]
        },
      ]
    },
    onNoteClick(note) {
      if (note.clicked) {
        document.querySelector('#note' + note.id).style["background-color"] = null;
      } else {
        document.querySelector('#note' + note.id).style["background-color"] = "#b9bec4";
      }
      note.clicked = !note.clicked;
    },
    onNoteDblClick(note) {
      console.log(note);
    }
  }

}
</script>

<style scoped>
.note-manager-title {
  text-align: center;
}
.buttons {
  margin:  10px;
}
.note-manager-crud-button {
  margin: 5px;
}
.notes-container {
  margin: 20px 10px;
  display: flex;
  flex-wrap: wrap
}
.note-card {
  min-width: 250px;
  width: 350px;
  margin: 20px;
  height: 250px;
  cursor: pointer;
}
.note-card:hover {
  background-color: #ebebeb;
}
.card-title {
  text-align: center;
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
  height: 28px;
}
.todo-list {
  height: 156px;
  overflow: hidden;
}
.todo-item {
  height: 1.5em;
  margin: 10px 20px 10px 30px;
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
}
</style>
