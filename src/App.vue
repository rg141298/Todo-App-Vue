<template>
  <div id="app" @click="clear()">
    <v-app>
      <v-main>
        <v-container style="max-width: 500px; margin-top: 5%">
          <v-text-field
            v-model="task"
            label="Add new ToDo?"
            solo
            @keydown.enter="create(task)"
          >
          </v-text-field>

          <h2 class="display-1 info--text pl-3">
            Total Tasks:&nbsp;
            <v-fade-transition leave-absolute>
              <span :key="`tasks-${tasks.length}`">
                {{ tasks.length }}
              </span>
            </v-fade-transition>
          </h2>

          <v-divider class="mt-3"></v-divider>

          <v-layout my-1 align-center>
            <strong class="mx-3 warning--text text--darken-3">
              Remaining: {{ remainingTasks }}
            </strong>

            <!-- Will be fixed for v1.2 -->
            <v-divider vertical></v-divider>

            <strong class="mx-3 success--text">
              Completed: {{ completedTasks }}
            </strong>

            <v-spacer></v-spacer>

            <v-progress-circular
              :value="progress"
              class="mr-2"
            ></v-progress-circular>
          </v-layout>

          <v-divider class="mb-3"></v-divider>

          <v-card v-if="tasks.length > 0">
            <v-list class="py-0">
              <template v-for="(task, i) in tasks">
                <v-divider v-if="i !== 0" :key="`${i}-divider`"></v-divider>

                <v-list-item
                  :key="`${i}-${task.text}`"
                  v-if="!task.done"
                  style="padding: 0px 10px"
                >
                  <v-list-item-action style="width: 100%; flex-direction: row ">
                    <v-checkbox v-model="task.done" color="info darken-3">
                      <span
                        slot="label"
                        class="ml-3 grey--text task"
                        :class="(task.done && 'grey--text') || 'text--primary'"
                      >
                        {{ task.text }}
                      </span>
                    </v-checkbox>
                    <ul style="width: 70px">
                      <v-icon
                        @click.prevent="editItem(task)"
                        style="margin-right: 10px; padding-left: 15px"
                        >mdi-pencil</v-icon
                      >
                      <v-icon @click="remove(i)">mdi-delete</v-icon>
                    </ul>
                  </v-list-item-action>

                  <v-spacer></v-spacer>

                  <v-scroll-x-transition>
                    <v-icon v-if="task.done" color="success">
                      mdi-check
                    </v-icon>
                  </v-scroll-x-transition>
                </v-list-item>
              </template>
            </v-list>
          </v-card>
        </v-container>

        <v-container
          style="max-width: 500px; margin-top: 5%"
          v-if="completedTasks > 0"
        >
          <h2 class="display-1 success--text pl-3">
            Completed Tasks:&nbsp;
            <v-fade-transition leave-absolute>
              <span :key="`tasks-${tasks.length}`">
                {{ completedTasks }}
              </span>
            </v-fade-transition>
          </h2>

          <v-divider class="mt-3"></v-divider>

          <strong class="mx-3 black--text">
            Completed: {{ completedTasks }}
          </strong>

          <v-spacer></v-spacer>

          <v-card v-if="completedTasks > 0">
            <v-list class="py-0">
              <template v-for="(task, i) in tasks">
                <v-divider v-if="i !== 0" :key="`${i}-divider`"></v-divider>

                <v-list-item v-if="task.done" :key="`${i}-${task.text}`">
                  <v-list-item-action style="width: 100%; flex-direction: row ">
                    <v-checkbox v-model="task.done" color="info darken-3">
                      <div
                        style="text-decoration: line-through"
                        slot="label"
                        :class="(task.done && 'grey--text') || 'text--primary'"
                        class="ml-3 completedTasks"
                        v-text="task.text"
                      ></div>
                    </v-checkbox>
                    <div>
                      <v-icon @click="remove(i)">mdi-delete</v-icon>
                    </div>
                  </v-list-item-action>

                  <v-spacer></v-spacer>

                  <v-scroll-x-transition>
                    <v-icon v-if="task.done" color="success">
                      mdi-check
                    </v-icon>
                  </v-scroll-x-transition>
                </v-list-item>
              </template>
            </v-list>
          </v-card>
        </v-container>
      </v-main>
    </v-app>
  </div>
</template>

<script>
export default {
  name: "App",
  data: () => ({
    tasks: [
      {
        id: 1,
        done: false,
        text: "ToDo",
      },
      {
        id: 2,
        done: false,
        text: "Arrays",
      },
      {
        id: 3,
        done: false,
        text: "ArrayList",
      },
      {
        id: 4,
        done: false,
        text: "JSON",
      },
      {
        id: 5,
        done: false,
        text: "Git",
      },
      {
        id: 6,
        done: false,
        text: "Vue",
      },
    ],
    nextTodoId: 7,
    task: null,
    editIndex: -1,
    editTodo: {},
    action: "",
  }),

  computed: {
    completedTasks() {
      return this.tasks.filter((task) => task.done).length;
    },
    progress() {
      return (this.completedTasks / this.tasks.length) * 100;
    },
    remainingTasks() {
      return this.tasks.length - this.completedTasks;
    },
  },
  watch: {
    action(newVal) {
      if (newVal === "edit") {
        this.editTodo.text = this.task;
      } else {
        this.task = null;
      }
    },
  },

  methods: {
    create(obj) {
      if (this.action === "edit") {
        this.tasks.forEach((task) => {
          if (task.id === this.editTodo.id) {
            let letters = /[a-zA-Z]/g;
            if (this.task !== null && letters.test(this.task)) {
              task.text = obj;
            } else {
              alert(
                "Invalid Input!! There must be atleast one alphabet in the input"
              );
            }
          }
        });
        this.task = "";
      } else {
        let letters = /[a-zA-Z]/g;
        if (this.task !== null && letters.test(this.task)) {
          this.tasks.push({
            id: this.nextTodoId++,
            done: false,
            text: this.task,
          });
        } else {
          alert(
            "Invalid Input!! There must be atleast one alphabet in the input"
          );
        }
        this.task = null;
      }
    },
    editItem(obj) {
      this.editTodo = obj;
      obj.text = this.editTodo.text;
      this.task = this.editTodo.text;
      this.action = "edit";
    },
    remove(index) {
      if (window.confirm("Are you Sure you want to delete?")) {
        this.tasks.splice(index, 1);
        this.task = null;
      }
    },
    clear() {
      if (this.task === "" || this.task === null) {
        this.action = "";
      }
    },
  },
};
</script>
<style>
.wrap-text {
  word-wrap: break-word;
}
.task {
  width: 250px; /* can be 100% ellipsis will happen when contents exceed it */
  display: inline-block;
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
}
.task:hover {
  white-space: normal;
  /* or: 
  width: auto;
  */
}
.completedTasks {
  width: 250px;
  display: inline-block;
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
}
.completedTasks:hover {
  white-space: normal;
}
</style>
