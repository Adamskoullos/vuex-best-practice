<template>
  <!-- Show the main content div if state has data and there is no error -->
  <div class="task-list" v-if="!st.isLoading && !st.error">
    <div class="task" v-for="todo in filterTodos" :key="todo.id">
      <p :class="{ strike: todo.complete }" v-if="!todo.update">
        {{ todo.text }}
      </p>
      <!-- The below form shows if the user has clicked to edit a task to update the text -->
      <form
        v-if="todo.update"
        @submit.prevent="handleUpdateText(todo, todo.text)"
      >
        <input type="text" v-model="todo.text" class="update-todo" />
        <div class="edit-buttons">
          <button>
            <span class="material-icons-outlined edit save">
              save
            </span>
          </button>
          <span
            class="material-icons-outlined edit back"
            v-if="todo.update"
            @click="handleUpdate(todo)"
          >
            backspace
          </span>
        </div>
      </form>
      <div class="todo-actions">
        <span
          class="material-icons outline"
          v-if="!todo.complete && !todo.update"
          @click="handleComplete(todo)"
          >check_box_outline_blank</span
        >
        <span
          class="material-icons"
          v-if="todo.complete && !todo.update"
          @click="handleComplete(todo)"
          >check_box</span
        >
        <span
          class="material-icons edit"
          @click="handleUpdate(todo)"
          v-if="!todo.update"
          >edit</span
        >
        <span
          class="material-icons edit"
          @click="handleDelete(todo)"
          v-if="!todo.update"
          >delete</span
        >
      </div>
    </div>
  </div>
  <!-- The below loader only shows on initial load while Heroku is spinning up after that while state.todos has a value it will not show -->
  <div v-if="st.isLoading && !st.error && !st.todos[0]">
    <Loader />
  </div>
  <!-- If there is an issue fetching data this message will show to let the user know -->
  <div v-if="st.error" class="error">
    <h1>{{ st.error }}</h1>
  </div>
</template>

<script>
  import { computed } from "@vue/runtime-core";
  import { useStore } from "vuex";
  import Loader from "../components/Loader";

  export default {
    props: ["todo", "st"],
    components: { Loader },
    setup(props) {
      const store = useStore();

      const handleComplete = (todo) => {
        store.dispatch(props.todo + "/toggleComplete", todo);
      };

      const handleDelete = (todo) => {
        store.dispatch(props.todo + "/deleteTodo", todo);
      };

      const handleUpdate = (todo) => {
        store.dispatch(props.todo + "/updateTodo", todo);
      };

      const handleUpdateText = (todo) => {
        store.dispatch(props.todo + "/updateTodoText", todo);
      };

      const filterTodos = computed(() => {
        return store.getters[props.todo + "/filterTodos"];
      });

      return {
        handleComplete,
        handleDelete,
        handleUpdate,
        handleUpdateText,
        filterTodos,
      };
    },
  };
</script>

<style lang="scss" scoped>
  div.error {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    h1 {
      margin: auto;
      font-size: 30px;
      font-weight: 400;
    }
  }
  .task-list {
    max-width: 900px;
    margin: auto;

    .task {
      //   background: gray;
      display: flex;
      align-items: center;
      justify-content: stretch;
      margin: 10px;

      p {
        flex: 1;
        margin: 10px 10px 10px 0;
        padding: 10px;
        border: 2px solid #ffff;
        border-bottom: 2px solid #e5e8ea;
        font-weight: 400;
        font-size: 18px;
        line-height: 30px;
        color: #505050;
      }
      p.strike {
        text-decoration: line-through;
      }
      form {
        flex: 1;
        display: flex;
        align-items: center;
        justify-content: stretch;

        input {
          flex: 1 1;
          margin-right: 5px;
          border: 2px solid #ecf0f3;
        }
        .edit-buttons {
          display: flex;
        }

        button {
          border: none;
          background: rgba(255, 255, 255, 0);
          display: flex;
          align-items: center;
          transition: all ease 0.2s;

          span {
            font-size: 34px !important;
          }
        }
      }
      input {
        flex: 1;
        padding: 10px;
        margin: 10px 10px 10px 0;
        border-radius: 6px;
        background: #ecf0f3;
        border: none;
        border: 2px solid #ecf0f3;
        font-weight: 400;
        font-size: 18px;
        line-height: 30px;
      }
      input:focus {
        outline: #aeadf6;
        border: 2px solid #aeadf6;
      }
      .todo-actions {
        display: flex;
        align-items: center;
      }

      span {
        margin: auto 5px;
        font-size: 32px;
        color: #81e597;
        border-radius: 4px;
        cursor: pointer;
        transition: all ease 0.2s;
      }
      span:hover {
        transform: scale(1.1);
        transition: all ease 0.2s;
      }

      span.outline {
        color: #e5e8ea;
        cursor: pointer;
      }
      span.outline:hover {
        color: #a8a9aa;
      }
      span.edit {
        font-size: 26px;
        color: #a8a9aa7a;
        cursor: pointer;
      }
      span.edit:hover {
        color: #a8a9aa;
      }
      span.edit.back {
        font-size: 28px;
        color: #a8a9aa7a;
        margin-right: 35px;
        margin-left: 0;
      }
      span.edit.back:hover {
        color: #a8a9aa !important;
      }
      span.edit.save {
        font-size: 28px;
        color: #81e597;
      }
      span.edit.save:hover {
        font-size: 28px;
        color: #81e597;
        transform: scale(1.07);
        transition: all ease 0.2s;
      }
    }
  }
  @media (min-width: 200px) and (max-width: 499px) {
    .task-list .task {
      flex-wrap: wrap;
      p {
        flex: 1 1;
        min-width: 250px;
      }
    }
    form {
      flex-wrap: wrap;
      input {
        flex: 1;
        min-width: 250px;
      }
      .edit-buttons {
        margin: auto;

        .back {
          margin-right: 0;
        }
      }
      span.edit.back {
        margin-right: 0 !important;
      }
      button {
        margin: auto;
      }
    }
    .todo-actions {
      margin: auto;
    }
  }
</style>
