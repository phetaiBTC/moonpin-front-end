<script setup lang="ts">
import type { TableColumnsType } from 'ant-design-vue';
import axios from 'axios';
import { onMounted,reactive, ref } from 'vue';
const open = ref<boolean>(false);
const open1 = ref<boolean>(false);

const showModal = () => {
  open.value = true;
};


interface DataUserInterface {
  name: string,
  email: string,
  email_verified_at:string,
  created_at:string
  updated_at:string
}
const dataUser = reactive<DataUserInterface[]>([])
onMounted(async() => {
  await axios.get('http://127.0.0.1:8000/api/user').then((res) => {
    for (let i = 0; i < res.data.length; i++) {
      dataUser.push({
        name: res.data[i].name,
        email: res.data[i].email,
        email_verified_at: res.data[i].id,
        created_at: res.data[i].created_at,
        updated_at: res.data[i].updated_at
      })
    }
  })
})
const columns: TableColumnsType = [
  { title: 'id', width: 50, dataIndex: 'email_verified_at', key: 'id', fixed: 'left' },
  { title: 'Full Name', width: 50, dataIndex: 'name', key: 'name', fixed: 'left' },
  { title: 'email', dataIndex: 'email', key: '1', width: 150 },
  { title: 'created_at', dataIndex: 'created_at', key: '3', width: 150 },
  { title: 'updated_at', dataIndex: 'updated_at', key: '4', width: 150 },
  {title: 'Action',key: 'operation',fixed: 'right',width: 100},
];
const addData=reactive({
  name: '',
  email: '',
  id:null,
  password: ''
})
const handleOk = async() => {
  await axios.post('http://127.0.0.1:8000/api/register',addData).then((res) => {
    alert("success")
    window.location.reload()
  })

};

const deleteuser = async(id:number)=>{
  await axios.delete(`http://127.0.0.1:8000/api/deleteuser/${id}`).then((res) => {
    alert(res.data.message)
    window.location.reload()
  })
}
const edituser=async(id:number)=>{
  await axios.get(`http://127.0.0.1:8000/api/edituser/${id}`).then((res)=>{
    addData.name=res.data.name
    addData.email=res.data.email
    addData.id=res.data.id
    // console.log(addData.id)
    open1.value=true
  })
}
const updateuser=async()=>{
  console.log('id : '+addData.id)
  await axios.put(`http://127.0.0.1:8000/api/updateuser/${addData.id}`,addData).then((res)=>{
    alert(res.data.name)
    window.location.reload()
  })
}
</script>

<template>
  <div>
    <a-button type="primary" @click="showModal" class="m-4">Open Modal</a-button>
    <a-modal v-model:open="open" title="Basic Modal" @ok="handleOk">
      <div class="flex flex-col">
        <input type="text" placeholder="name" v-model="addData.name" class="p-2 text-white m-2">
        <input type="text" placeholder="email" v-model="addData.email" class="p-2 text-white m-2">
        <input type="password" placeholder="password" v-model="addData.password" class="p-2 text-white m-2">
      </div>
    </a-modal>
    <a-modal v-model:open="open1" title="Basic Modal" @ok="updateuser">
      <div class="flex flex-col">
        <input type="text" placeholder="name" v-model="addData.name" class="p-2 text-white m-2">
        <input type="text" placeholder="email" v-model="addData.email" class="p-2 text-white m-2">
        <input type="password" placeholder="password" v-model="addData.password" class="p-2 text-white m-2">
      </div>
    </a-modal>
  </div>
  <a-table :columns="columns" :data-source="dataUser" :scroll="{ x: 1500, y: 500 }">
    <template #bodyCell="{ column,record }">
      <template v-if="column.key === 'operation'">
        <div>
          <a-button type="primary" @click="edituser(record.email_verified_at)">Edit</a-button>
          <a-button type="primary" @click="deleteuser(record.email_verified_at)">Delete</a-button>
        </div>
      </template>
    </template>
  </a-table>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}

.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
