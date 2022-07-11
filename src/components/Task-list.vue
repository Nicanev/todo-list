<template>
  <div class="todo-list">
    <div
      class="background-transparent"
      :class="{ active: isContextMenyActive }"
      @click="closeContextMenu"
    ></div>
    <h1 class="green">Todo List</h1>
    <h3>Сreate your to-do list</h3>
    <div class="new-task">
      <input
        type="text"
        v-model="newTask"
        placeholder="New task"
        v-on:keyup.enter="addNewTask"
      />
      <button @click="addNewTask">Add</button>
    </div>

    <div class="list" id="list">
      <ul>
        <li
          class="list__item"
          v-for="task in tasks"
          v-bind:key="task"
          @click="selectItem($event, tasks.indexOf(task))"
          @click.right="
            openContextMenu($event),
              selectItem($event, tasks.indexOf(task)),
              (task.isSelect = true)
          "
          :class="{ 'selected-item': task.isSelect }"
        >
          <div class="item__text">
            <div class="item__title" :class="{ strike: task.isDone }">
              {{ task.title }}
            </div>
            <div class="item__time">{{ task.time }}</div>
          </div>
        </li>
      </ul>
    </div>

    <div
      class="context_menu"
      :class="{ active: isContextMenyActive }"
      :style="{ left: menuPositionX, top: menuPositionY }"
    >
      <ul>
        <li @click="performTask">
          <a href="#">{{ names.done }}</a>
        </li>
        <li @click="deleteTask"><a href="#">Delete task</a></li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: [
        { title: "One task", time: "12:15:25", isSelect: false, isDone: false },
      ],
      newTask: "",
      isContextMenyActive: false,
      menuPositionX: "",
      menuPositionY: "",
      names: {
        done: "Done",
      },
    };
  },
  mounted() {
    window.addEventListener("keydown", (e) => {
      if (e.key == "Escape") {
        this.closeContextMenu();
      }
    });
  },
  methods: {
    addNewTask() {
      if (this.newTask != "") {
        let date = new Date();
        let dateString =
          date.getHours() + ":" + date.getMinutes() + ":" + date.getSeconds();
        this.tasks.push({
          title: this.newTask,
          time: dateString,
          isSelect: false,
          isDone: false,
        });
        this.newTask = "";
      }
    },
    openContextMenu(e) {
      e.preventDefault();
      let menuPosition = this.getPosition(e);

      console.log(menuPosition);
      this.menuPositionX = menuPosition.x + "px";
      this.menuPositionY = menuPosition.y + "px";
      this.isContextMenyActive = true;
    },
    selectItem(e, item) {
      let selectItem = this.tasks[item];
      if (selectItem.isDone) {
        this.names.done = "Undone";
      } else {
        this.names.done = "Done";
      }
      if (!e.ctrlKey && !e.shiftKey) {
        this.tasks.forEach(function (i, index) {
          if (i.isSelect && index != item) {
            i.isSelect = false;
          }
        });
      }
      selectItem.isSelect = !selectItem.isSelect;

      let tasks = this.tasks;
      if (e.shiftKey) {
        this.tasks.forEach(function (i, index) {
          if (tasks[index].isSelect) {
            if (item < index) {
              while (item != index) {
                tasks[index].isSelect = true;
                index--;
              }
            } else {
              while (item != index) {
                tasks[index].isSelect = true;
                index++;
              }
            }

            return;
          }
        });
        tasks = this.tasks;
      }
      console.log(item);
    },
    performTask() {
      let tasks = this.tasks;
      tasks.forEach(function (item, index) {
        if (item.isSelect == true) {
          item.isDone = !item.isDone;
          if (tasks[index].isDone) {
            tasks.splice(index, 1);
            item.isSelect = false;
            tasks.push(item);
          } else {
            tasks.splice(index, 1);
            item.isSelect = false;
            tasks.unshift(item);
          }
        }
      });
      this.isContextMenyActive = false;
      this.tasks = tasks;
    },
    deleteTask() {
      let tasks = this.tasks;
      tasks.forEach(function (item, index) {
        if (item.isSelect) {
          tasks.splice(index, 1);
        }
      });
      this.isContextMenyActive = false;
      this.tasks = tasks;
    },
    closeContextMenu() {
      this.isContextMenyActive = false;
      this.tasks.forEach(function (item) {
        if (item.isSelect == true) {
          item.isSelect = false;
          console.log(item);
        }
      });
    },
    getPosition(e) {
      let posx = e.pageX - 350;
      let posy = e.pageY - 30;

      return {
        x: posx,
        y: posy,
      };
    },
  },
};
</script>

<style>
.list {
  margin-top: 20px;
}

.background-transparent {
  display: none;
  min-width: 100vw;
  min-height: 100vh;
  margin: 0 calc(-50vw + 50%);

  position: absolute;
  z-index: 9;

  background-color: transparent;
}

.list ul {
  margin: 0;
  padding: 0;
  list-style: none;
}
.list ul > li {
  margin-bottom: 15px;
}
.new-task {
  display: grid;
  margin-top: 20px;
  grid-template-columns: 1fr 120px;
  align-items: center;
  gap: 10px;
}

.list__item {
  border: 1px solid rgba(128, 128, 128, 0.397);
  padding: 15px;
  border-radius: 2px;
}
.strike {
  text-decoration: line-through;
  color: rgb(104, 100, 100);
}
.selected-item {
  background-color: #2b2929;
}
.item__text {
  display: grid;
  grid-template-columns: 1fr 1fr;
  align-items: center;
}
.item__title {
  justify-self: start;
}
.item__time {
  justify-self: end;
}
.context_menu {
  display: none;
  background-color: rgb(27, 27, 27);
  min-width: 200px;
  position: absolute;
  z-index: 10;
}
.active {
  display: block;
}
.context_menu ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
.context_menu li {
  padding: 15px;
  cursor: pointer;
}
.context_menu li:hover {
  background-color: rgb(44, 44, 44);
}
.context_menu a {
  color: white;
}
.context_menu a:hover {
  background-color: transparent;
  color: white;
}

button {
  height: 40px;
  color: white;
  border: none;
  background-color: #28a745;
  font-size: 20px;
  border-radius: 2px;
  transition: 0.4s;
  cursor: pointer;
}
input {
  height: 40px;
  border: none;
  text-indent: 10px;
  color: white;
  border-radius: 2px;
  background-color: #2b2929;
  transition: 0.4s;
  outline: none;
}
@media (hover: hover) {
  input:hover {
    background-color: #302d2d;
  }
  button:hover {
    background-color: #2cc04e;
  }
  .list__item:hover {
    background-color: #2b2929;
    border: 1px solid #2b2929;
  }
}
</style>
