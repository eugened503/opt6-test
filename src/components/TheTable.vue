<template>
  <div class="table-wrapper">
    <div>
      <button type="button" class="table-wrapper__add-row-button" @click="addRow">
        <CrossIcon />
        Добавить строку
      </button>
    </div>
    <div>Сохранить изменения
      <button type="button">
        <gearWheel />
      </button>
      <!-- <p>{{ selectedNameUnits }}</p> -->
    </div>
    <table>
      <thead>
        <tr>
          <th scope="col" v-for="(item) in col">
            <span @mouseover="mouseOver" @mouseleave="mouseLeave">
              {{ item.label }}
            </span>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in tableDataConverter" :key="item.id">
          <th scope="row" v-for="el in item">
            <div v-if="item.nameUnit !== el && item.id !== el"> {{ el }}</div>
            <div v-if="item.id === el">
              <span>{{ index + 1 }}</span>
              <button class="button-col" type="button" @mouseover="mouseOverSelectRow"
                @mouseleave="mouseLeaveSelectRow">
                <threeLines />
              </button>
            </div>
            <div v-if="item.action === el && item.action === null" class="action-buttons">
              <button type="button" @click="isDeleteId = isDeleteId === item.id ? null : item.id">
                <threeDots />
              </button>
              <button v-if="item.id === isDeleteId" type="button" class="delete-button" @click="deleteProduct">
                удалить
              </button>
            </div>
            <div v-if="item.nameUnit && item.nameUnit === el">
              <v-select v-model="selectedNameUnits[index]" :options="options" />
            </div>
          </th>
        </tr>
      </tbody>
    </table>
    <TheBasket />
  </div>
</template>

<script setup>
import Sortable from "sortablejs";
import { onMounted, ref, nextTick, watch, toRef, reactive, computed } from "vue";
import _ from 'lodash';
import gearWheel from "@/assets/images/gearWheel.svg";
import threeLines from "@/assets/images/threeLines.svg";
import threeDots from "@/assets/images/threeDots.svg";
import CrossIcon from "@/assets/images/cross.svg";
import { col, tableData, options } from '@/assets/data/data.js';
import { triangle } from '@/assets/constants/constants.js';
import TheBasket from '@/components/TheBasket.vue';

// эмиты
// const emit = defineEmits(['addRow']);
// const emit = defineProps({
//   addRow: Function,
// })

// данные
const isSelect = ref(false);
const isDeleteId = ref('');
const isSelectRowEvent = ref({});
const dropCol = toRef(reactive(_.cloneDeep(col)));
const tableDataCopy = toRef(reactive(_.cloneDeep(tableData)));
const tableDataConverter = toRef(reactive(_.cloneDeep(tableData)));
const columnDropSortable = toRef(reactive({}));
const rowDropSortable = toRef(reactive({}));
const selectedNameUnits = toRef(reactive(_.cloneDeep(options)));

// вычисляемые св-ва
const selectedNameUnitsComputed = computed(() => tableDataConverter.value.map(item => item.nameUnit));

// методы
const rowObj = () => {
  const keys = Object.keys(tableDataConverter.value[0]);
  const obj = keys.reduce((acc, curr) => (acc[curr] = '', acc), {});
  obj.id = getUniqId();
  obj.action = null;
  obj.nameUnit = "Выберите значение";

  return obj;
};

const addRow = () => {
  selectedNameUnits.value.push(rowObj().nameUnit);
  tableDataConverter.value.push(rowObj());
};

const deleteProduct = () => {
  const newTableData = tableDataConverter.value.filter(item => item.id !== isDeleteId.value);
  tableDataConverter.value = newTableData;
};

const getUniqId = () => String(Math.floor(new Date().valueOf() * Math.random()));

const getProduct = (value) => tableDataCopy.value.find((item) => item.nameUnit === value);

const addDataProduct = () => {
  const newTableData = [];
  selectedNameUnits.value.forEach((item, x) => {
    tableDataConverter.value.forEach((_, y) => {
      if (x === y) {
        const product = getProduct(item) || rowObj();
        const newEl = { ...product, id: getUniqId() };
        newTableData.push(newEl);
      }
    });
  });

  tableDataConverter.value = newTableData;
  nextTick(() => addTriangleIcon());
};

const rowDrop = () => {
  const tbody = document.querySelector("tbody");
  rowDropSortable.value = Sortable.create(tbody, {
    disabled: true,
    animation: 180,
    onEnd({ newIndex, oldIndex }) {
      const currRow = tableDataConverter.value.splice(oldIndex, 1)[0];
      tableDataConverter.value.splice(newIndex, 0, currRow);
      isSelect.value = false;
      selectedNameUnits.value = selectedNameUnitsComputed.value;
    },
  });
};

const columnDrop = () => {
  const wrapperTr = document.querySelector("tr");
  columnDropSortable.value = Sortable.create(wrapperTr, {
    disabled: true,
    animation: 180,
    delay: 0,
    onEnd: (evt) => {
      const oldItem = dropCol.value[evt.oldIndex];
      dropCol.value.splice(evt.oldIndex, 1);
      dropCol.value.splice(evt.newIndex, 0, oldItem);
      tableDataConverter.value = converter(tableDataConverter.value);
      tableDataCopy.value = converter(tableDataCopy.value);
    },
  });
};

const converter = (arr) => {
  const res = [];
  const size = arr.length;
  const subarray = [];

  col.forEach((_, index) => {
    arr.forEach((tableDataItem) => {
      const item = {
        [dropCol.value[index].prop]: tableDataItem[dropCol.value[index].prop],
      };
      res.push(item);
    });
  });

  for (let i = 0; i < Math.ceil(res.length / size); i++) {
    subarray[i] = res.slice(i * size, i * size + size);
  }

  const mergedArray = Array.from(
    {
      length: subarray[0].length,
    },
    (_, index) => Object.assign({}, ...subarray.map(({ [index]: obj }) => obj)),
  );

  return mergedArray;
};

const resizableTable = () => {
  let thElm;
  let startOffset;

  Array.prototype.forEach.call(
    document.querySelectorAll("table th"),
    function (th) {
      th.style.position = "relative";

      const grip = document.createElement("div");
      grip.innerHTML = "&nbsp;";
      grip.style.top = 0;
      grip.style.right = 0;
      grip.style.bottom = 0;
      grip.style.width = "5px";
      grip.style.position = "absolute";
      grip.style.cursor = "col-resize";

      grip.addEventListener("mousedown", function (e) {
        thElm = th;
        startOffset = th.offsetWidth - e.pageX;
      });

      th.appendChild(grip);
    },
  );

  document.addEventListener("mousemove", function (e) {
    if (thElm) {
      thElm.style.width = startOffset + e.pageX + "px";
    }
  });

  document.addEventListener("mouseup", function () {
    thElm = undefined;
  });
};

const mouseOver = () => {
  columnDropSortable.value.options.disabled = false;
};

const mouseLeave = () => {
  columnDropSortable.value.options.disabled = true;
};

const mouseOverSelectRow = () => {
  rowDropSortable.value.options.disabled = false;
};

const mouseLeaveSelectRow = () => {
  rowDropSortable.value.options.disabled = true;;
};

const buttonListeners = () => {
  document.addEventListener("mousedown", function (e) {
    if (e.target.classList.contains('button-col')) {
      e.target.parentElement.parentElement.parentElement.classList.add('active');
      isSelectRowEvent.value = e;
      isSelect.value = true;
    }
  });

  document.addEventListener("mouseup", function (e) {
    if (e.target.classList.contains('button-col')) {
      isSelect.value = false;
    }
  });
};

const addTriangleIcon = () => {
  const actionAll = document.querySelectorAll('.vs__actions');
  actionAll.forEach((action) => {
    action.innerHTML = triangle;
    const svg = action.querySelector('svg');
    svg.style.width = '5px';
    svg.style.height = '5px';
    svg.style.transform = 'rotate(-90deg)';
  });
};

// хуки
onMounted(() => {
  rowDrop();
  columnDrop();
  resizableTable();
  nextTick(() => {
    buttonListeners();
    addTriangleIcon();
  });
});

// наблюдатели
watch(isSelect, (newIsSelect) => {
  if (!newIsSelect) {
    isSelectRowEvent.value.target.parentElement.parentElement.parentElement.classList.remove("active");
  }
});

watch(tableDataConverter, (newTableDataConverter) => {
  // console.log('newTableDataConverter', newTableDataConverter);
}, { deep: true });

watch(selectedNameUnits, (newsSelectedNameUnits) => {
  if (newsSelectedNameUnits) {
    // console.log('newsSelectedNameUnits', newsSelectedNameUnits);
    addDataProduct();
  }
}, { deep: true });
</script>

<style scoped lang="scss">
.table-wrapper {
  &__add-row-button {
    background-color: aquamarine;
    padding: 10px;
    margin: 10px;
    cursor: pointer;
  }

  table {
    width: 100%;
    border-top: 7px solid rgb(43, 129, 17);
    border-collapse: collapse;
    text-align: center;
    margin-bottom: 20px;
    border: 1px solid #dddddd;

    thead {
      th {
        cursor: pointer;
      }

      span {
        display: block;
      }

      p {
        top: 0px;
        right: 0px;
        bottom: 0px;
        width: 5px;
        position: absolute;
        cursor: col-resize;
        z-index: 1;
      }
    }

    td {
      text-align: left;
      padding: 10px;
      border: 3px solid rgb(43, 129, 17);
    }

    th {
      text-align: center;
      padding: 10px;
      border: 3px solid rgb(43, 129, 17);
    }

    tbody {
      button {
        width: 20px;
        height: 20px;
        cursor: pointer;
      }
    }

    .active {
      border: red 4px dashed;
    }

    .rotate {
      width: 5px;
      height: 5px;
      transform: rotate(-90deg);
    }

    .action-buttons {
      display: flex;
    }

    .delete-button {
      background-color: brown;
      flex: 1;
    }
  }
}
</style>

<style lang="scss">
.vs {
  &__clear {
    display: none;
  }

  &__selected {
    opacity: 1 !important;
  }

  &__search {}

  &__open-indicator {
    display: none;
  }

  &__dropdown-menu {}

  .vs1 {
    &__listbox {}
  }
}
</style>