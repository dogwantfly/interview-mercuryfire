<template>
  <q-page class="row q-pt-xl">
    <div class="full-width q-px-xl">
      <div class="q-mb-xl">
        <q-input v-model="tempData.name" label="姓名" />
        <q-input v-model="tempData.age" label="年齡" />

        <q-btn
          color="primary"
          class="q-mt-md"
          @click="handleClickAdd(tempData)"
          v-if="btnAction === 'add'"
          >新增</q-btn
        >
        <q-btn
          color="primary"
          class="q-mt-md"
          @click="handleClickEdit(tempData)"
          v-if="btnAction === 'edit'"
          >編輯</q-btn
        >
      </div>

      <q-table
        flat
        bordered
        ref="tableRef"
        :rows="blockData"
        :columns="(tableConfig as QTableProps['columns'])"
        row-key="id"
        hide-pagination
        separator="cell"
        :rows-per-page-options="[0]"
      >
        <template v-slot:header="props">
          <q-tr :props="props">
            <q-th v-for="col in props.cols" :key="col.name" :props="props">
              {{ col.label }}
            </q-th>
            <q-th></q-th>
          </q-tr>
        </template>

        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td
              v-for="col in props.cols"
              :key="col.name"
              :props="props"
              style="min-width: 120px"
            >
              <div>{{ col.value }}</div>
            </q-td>
            <q-td class="text-right" auto-width v-if="tableButtons.length > 0">
              <q-btn
                @click="handleClickOption(btn, props.row)"
                v-for="(btn, index) in tableButtons"
                :key="index"
                size="sm"
                color="grey-6"
                round
                dense
                :icon="btn.icon"
                class="q-ml-md"
                padding="5px 5px"
              >
                <q-tooltip
                  transition-show="scale"
                  transition-hide="scale"
                  anchor="top middle"
                  self="bottom middle"
                  :offset="[10, 10]"
                >
                  {{ btn.label }}
                </q-tooltip>
              </q-btn>
            </q-td>
          </q-tr>
        </template>
        <template v-slot:no-data="{ icon }">
          <div
            class="full-width row flex-center items-center text-primary q-gutter-sm"
            style="font-size: 18px"
          >
            <q-icon size="2em" :name="icon" />
            <span> 無相關資料 </span>
          </div>
        </template>
      </q-table>
    </div>
    <q-dialog v-model="comfirmDelete" persistent>
      <q-card>
        <q-card-section class="row items-center">
          <q-avatar icon="signal_wifi_off" color="primary" text-color="white" />
          <span class="q-ml-sm">確定刪除？</span>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="取消" color="primary" v-close-popup />
          <q-btn
            flat
            label="確定刪除"
            color="danger"
            @click="handleClickDelete(deleteId)"
          />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script setup lang="ts">
import axios from 'axios';
import { QTableProps } from 'quasar';
import { ref } from 'vue';
interface btnType {
  label: string;
  icon: string;
  status: string;
}
interface inputData {
  name: string;
  age: string;
  id?: number;
}
const blockData = ref([
  {
    name: 'test',
    age: 25,
    id: new Date().getTime(),
  },
]);
const tableConfig = ref([
  {
    label: '姓名',
    name: 'name',
    field: 'name',
    align: 'left',
  },
  {
    label: '年齡',
    name: 'age',
    field: 'age',
    align: 'left',
  },
]);
const tableButtons = ref([
  {
    label: '編輯',
    icon: 'edit',
    status: 'edit',
  },
  {
    label: '刪除',
    icon: 'delete',
    status: 'delete',
  },
]);

const tempData = ref({
  name: '',
  age: '',
});
const btnAction = ref('add');
const comfirmDelete = ref(false);
const deleteId = ref();
function handleClickOption(btn: btnType, data: inputData) {
  if (btn.status === 'edit') {
    tempData.value = { ...data };
    btnAction.value = 'edit';
  } else if (btn.status === 'delete') {
    comfirmDelete.value = true;
    deleteId.value = data.id;
  }
}
function handleClickAdd(data: inputData) {
  blockData.value.push({
    age: parseInt(data.age),
    name: data.name,
    id: new Date().getTime(),
  });
  tempData.value = {
    name: '',
    age: '',
  };
}
function handleClickEdit(data: inputData) {
  const index = blockData.value.findIndex((item) => item.id === data.id);
  if (index !== -1) {
    blockData.value[index] = {
      ...data,
      age: parseInt(data.age),
      id: new Date().getTime(),
    };
    tempData.value = {
      name: '',
      age: '',
    };
    btnAction.value = 'add';
  }
}
function handleClickDelete(id: number) {
  blockData.value = blockData.value.filter((item) => item.id !== id);
  comfirmDelete.value = false;
}
</script>

<style lang="scss" scoped>
.q-table th {
  font-size: 20px;
  font-weight: bold;
}

.q-table tbody td {
  font-size: 18px;
}
</style>
