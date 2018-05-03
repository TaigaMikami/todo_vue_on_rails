<template>
  <div>
    <div class="row task-form">
      <div class="col s10 m11">
        <input type="text" class="form-control" placeholder="Add your task" v-model="newTask">
      </div>
      <div class="col s2 m1">
        <button class="btn-floating waves-effect waves-light red" @click="createTask">
          <i class="material-icons">add</i>
        </button>
      </div>
    </div>
    <!--リスト表示部分-->
    <div>
      <ul class="collection">
        <li class="collection-item" v-for="task in tasks" v-if="!task.is_done" :id="'row_task_'+task.id">
          <input type="checkbox" @change="doneTask(task.id)" :id="'task_'+task.id" />
          <label :for="'task_'+task.id">{{ task.name }}</label>
        </li>
      </ul>
    </div>
    <!--完了済みタスク表示ボタン-->
    <div class="btn" @click="displayFinishedTasks">Display finished tasks</div>
    <!--完了済みタスク一覧-->
    <div id="finished-tasks" class="display_none">
      <ul class="collection">
        <li class="collection-item" v-for="task in tasks" v-if="task.is_done" :id="'row_task_'+task.id">
          <input type="checkbox" :id="'task_'+task.id" checked="checked"/>
          <label :for="'task_'+task.id" class="line-through">{{ task.name }}</label>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    data: function () {
      return {
        tasks: [],
        newTask: ''
      }
    },
    mounted: function() {
      this.fetchTasks();
    },
    methods: {
      fetchTasks: function () {
        axios.get('/api/tasks').then((response) => {
          for(var i=0; i<response.data.tasks.length; i++){
            this.tasks.push(response.data.tasks[i]);
          }
        }, (error) => {
          console.log(error);
        });
      },
      displayFinishedTasks: function () {
        document.querySelector('#finished-tasks').classList.toggle('display_none');
      },
      createTask: function () {
        if(!this.newTask) return;

        axios.post('/api/tasks', { task: { name: this.newTask } }).then((response) => {
          this.tasks.unshift(response.data.task);
          this.newTask = '';
        }, (error) => {
          console.log(error);
        });
      },
      doneTask: function (task_id) {
        axios.put('/api/tasks/' + task_id, { task: { is_done: 1 } }).then((response) => {
          this.moveFinishedTask(task_id);
        }, (error) => {
          console.log(error);
        });
      },
      moveFinishedTask: function (task_id) {
        var el = document.querySelector('#row_task_' + task_id);
        var el_clone = el.cloneNode(true); // DOMをクローンしておく
        el.classList.add('display_none'); // 未完了の方を先に非表示にする
        el_clone.getElementsByTagName('input')[0].checked = 'checked';
        el_clone.getElementsByTagName('label')[0].classList.add('line-through');
        el_clone.getElementsByTagName('label')[0].classList.remove('word-color-black');
        var li = document.querySelector('#finished-tasks > ul > li:first-child');
        document.querySelector('#finished-tasks > ul').insertBefore(el_clone, li);
      }
    }
  }
</script>

<style scoped>
  [v-cloak] {
    display: none;
  }
  .display_none {
    display: none;
  }
  .line-through {
    text-decoration: line-through;
  }

</style>