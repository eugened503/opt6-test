<template>
  <div class="table-wrapper">
    <div class="table-wrapper__header">
      <button
        type="button"
        class="table-wrapper__add-row-button"
        @click="addRow"
      >
        <CrossIcon />
        Добавить строку
      </button>
    </div>
    <div class="table-wrapper__body">
      <div class="table-wrapper__body-header">
        <div class="table-wrapper__body-header-content">
          <button
            type="button"
            class="table-wrapper__save-button"
            :disabled="!isSave"
            @click="fetchData"
          >
            Сохранить изменения
          </button>
          <button type="button">
            <gearWheel />
          </button>
        </div>
      </div>
      <table class="table">
        <thead>
          <tr>
            <th scope="col" v-for="item in col" :class="[item.prop]">
              <span
                v-if="item.label !== 'id' && item.label !== 'action'"
                @mouseover="mouseOver"
                @mouseleave="mouseLeave"
              >
                {{ item.label }}
              </span>
              <span v-else @mouseover="mouseOver" @mouseleave="mouseLeave">
                &nbsp;&nbsp;
              </span>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in tableDataConverter" :key="item.id">
            <td scope="row" v-for="(el, indexEl) in item" :class="[indexEl]">
              <div>
                <span v-if="item.price === el && '' !== el" class="title"
                  >Цена</span
                >
                <span v-if="item.quantity === el && '' !== el" class="title"
                  >Кол-во</span
                >
                <span v-if="item.productName === el && '' !== el" class="title"
                  >Название товара</span
                >
                <span v-if="item.total === el && '' !== el" class="title"
                  >Итого</span
                >
                <div
                  v-if="
                    item.nameUnit !== el &&
                    item.id !== el &&
                    item.action !== el &&
                    '' !== el
                  "
                  class="table__cell-content"
                >
                  {{ el }}
                  <button
                    v-if="item.productName === el"
                    class="table__cell-button"
                  >
                    <triangleSvg class="rotate" />
                  </button>
                </div>
                <div class="table__buttons" v-if="item.id === el">
                  <span class="title">Номер строки</span>
                  <button class="table__button-col" type="button">
                    <threeLines />
                    <span>{{ index + 1 }}</span>
                  </button>
                </div>
                <div
                  v-if="item.action === el && item.action === null"
                  class="table__action-buttons"
                >
                  <span class="title">Действие</span>
                  <button
                    type="button"
                    @click.stop="
                      isDeleteId = isDeleteId === item.id ? null : item.id
                    "
                  >
                    <threeDots />
                  </button>
                  <button
                    v-if="item.id === isDeleteId"
                    type="button"
                    class="table__delete-button"
                    @click.stop="deleteProduct(index)"
                  >
                    удалить
                  </button>
                </div>
                <div
                  v-if="item.nameUnit && item.nameUnit === el"
                  class="table__select"
                  :class="{
                    'select-display': item.nameUnit === 'Выберите значение',
                  }"
                >
                  <span class="title">Наименование единицы</span>
                  <v-select
                    v-model="selectedNameUnits[index]"
                    :options="options"
                  />
                </div>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
      <TheBasket />
    </div>
  </div>
</template>

<script setup>
import Sortable from "sortablejs";
import {
  onMounted,
  ref,
  nextTick,
  watch,
  toRef,
  reactive,
  computed,
} from "vue";
import _ from "lodash";
import gearWheel from "@/assets/images/gearWheel.svg";
import triangleSvg from "@/assets/images/triangle.svg";
import threeLines from "@/assets/images/threeLines.svg";
import threeDots from "@/assets/images/threeDots.svg";
import CrossIcon from "@/assets/images/cross.svg";
import { col, tableData, options } from "@/assets/data/data.js";
import { triangle } from "@/assets/constants/constants.js";
import TheBasket from "@/components/TheBasket.vue";

// пропсы
const props = defineProps({
  checkedNamesArr: Array,
});

// данные
const isSelect = ref(false);
const isSave = ref(false);
const isCheckedNames = ref(reactive([]));
const hideElementIndex = ref(reactive([]));
const isDeleteId = ref("");
const isSelectRowEvent = ref({});
const dropCol = toRef(reactive(_.cloneDeep(col)));
const tableDataCopy = toRef(reactive(_.cloneDeep(tableData)));
const tableDataConverter = toRef(reactive(_.cloneDeep(tableData)));
const columnDropSortable = toRef(reactive({}));
const rowDropSortable = toRef(reactive({}));
const selectedNameUnits = toRef(reactive(_.cloneDeep(options)));

// вычисляемые св-ва
const selectedNameUnitsComputed = computed(() =>
  tableDataConverter.value.map((item) => item.nameUnit),
);

// методы
const getCheckedNames = (checkedNames) => {
  isCheckedNames.value = checkedNames;
  const columnNames = dropCol.value.map((item) => item.prop);
  const keys = Object.keys(checkedNames);
  const hideElementKeys = keys.filter((key) => !checkedNames[key]);
  const hideElementIndexArr = hideElementKeys.map(
    (item) => columnNames.indexOf(item) + 1,
  );

  hideElementIndex.value = [...hideElementIndexArr];
  columnVisibility();
  columnVisibilityIndex();
};

const columnVisibilityIndex = () => {
  if (hideElementIndex.value.length)
    hideElementIndex.value.forEach((index) => columnVisibilityNone(index));
};

const columnVisibilityNone = (columnIndex) => {
  const cols = document.querySelectorAll(
    `td:nth-child(${columnIndex}), th:nth-child(${columnIndex})`,
  );
  cols.forEach(function (col) {
    col.style.display = "none";
  });
};

function columnVisibility() {
  const cols = document.querySelectorAll("td, th");
  cols.forEach(function (col) {
    col.style.display = "";
  });
}

const rowObj = () => {
  const keys = Object.keys(tableDataConverter.value[0]);
  const obj = keys.reduce((acc, curr) => ((acc[curr] = ""), acc), {});
  obj.id = getUniqId();
  obj.action = null;
  obj.nameUnit = "Выберите значение";

  return obj;
};

const addRow = () => {
  selectedNameUnits.value.push(rowObj().nameUnit);
  tableDataConverter.value.push(rowObj());
  nextTick(() => columnVisibilityIndex());
};

const deleteProduct = (index) => {
  selectedNameUnits.value.splice(index, 1);
  const newTableData = tableDataConverter.value.filter(
    (item) => item.id !== isDeleteId.value,
  );
  tableDataConverter.value = newTableData;
  nextTick(() => columnVisibilityIndex());
};

const getUniqId = () =>
  String(Math.floor(new Date().valueOf() * Math.random()));

const getProduct = (value) =>
  tableDataCopy.value.find((item) => item.nameUnit === value);

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
  nextTick(() => {
    addTriangleIcon();
    columnVisibilityIndex();
  });
};

const rowDrop = () => {
  const tbody = document.querySelector("tbody");
  rowDropSortable.value = Sortable.create(tbody, {
    animation: 180,
    delay: 0,
    onEnd({ newIndex, oldIndex }) {
      const currRow = tableDataConverter.value.splice(oldIndex, 1)[0];
      tableDataConverter.value.splice(newIndex, 0, currRow);
      isSelect.value = false;
      selectedNameUnits.value = selectedNameUnitsComputed.value;
      columnVisibilityIndex();
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

      nextTick(() => {
        columnVisibility();
        getCheckedNames(isCheckedNames.value);
      });
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
      grip.style.userSelect = "none";

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

const buttonListeners = () => {
  document.addEventListener("mousedown", function (e) {
    if (e.target.classList.contains("table__button-col")) {
      e.target.parentElement.parentElement.parentElement.parentElement.classList.add(
        "active",
      );
      isSelectRowEvent.value = e;
      isSelect.value = true;
    }
  });

  document.addEventListener("mouseup", function (e) {
    if (e.target.classList.contains("table__button-col")) {
      isSelect.value = false;
    }
  });
};

const addTriangleIcon = () => {
  const actionAll = document.querySelectorAll(".vs__actions");
  actionAll.forEach((action) => {
    action.innerHTML = triangle;
    const svg = action.querySelector("svg");
    svg.style.width = "5px";
    svg.style.height = "5px";
    svg.style.transform = "rotate(-90deg)";
  });
};

// отправка данных на бекенд
const fetchData = () => {
  const url = "https://jsonplaceholder.typicode.com/posts/";

  fetch(url, {
    method: "POST",
    body: JSON.stringify(tableDataConverter.value),
    headers: { "Content-Type": "application/json" },
  })
    .then((res) => res.json())
    .then((data) => {
      isSave.value = false;
      console.log(data);
    })
    .catch((error) => console.log("error", error));
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
    isSelectRowEvent.value.target.parentElement.parentElement.parentElement.parentElement.classList.remove(
      "active",
    );
  }
});

watch(
  selectedNameUnits,
  (newsSelectedNameUnits) => {
    if (newsSelectedNameUnits) {
      addDataProduct();
    }
  },
  { deep: true },
);

watch(
  tableDataConverter,
  (newTableDataConverter) => {
    if (newTableDataConverter) {
      isSave.value = true;
    }
  },
  { deep: true },
);

watch(
  props,
  (newProps) => {
    getCheckedNames(newProps.checkedNamesArr[0]);
  },
  { deep: true },
);
</script>

<style scoped lang="scss">
.table-wrapper {
  margin: 25px 0 0;

  @media screen and (max-width: 767px) {
    margin: 30px 0 0;
  }

  &__header {
    padding: 17px 25px 17px 25px;
    border-radius: 10px;
    box-shadow: 0 5px 20px rgba(0, 0, 0, 0.07);
    border: solid 1px #eeeff1;
    background-color: #fff;

    @media screen and (max-width: 767px) {
      padding: 17px 25px 19px 25px;
    }
  }

  &__add-row-button {
    display: flex;
    align-items: center;
    background-color: #2f8cff;
    border-radius: 5px;
    padding: 7px 16px 8px 13px;
    color: #fff;
    font-size: 14px;
    gap: 3px;
    cursor: pointer;
  }

  &__save-button {
    font-size: 12px;
    color: #2f8cff;

    &:disabled {
      color: #a6b7d4;
    }
  }

  &__body {
    margin: 25px 0 0 0;
    padding: 0 0 20px;
    box-shadow: 0 5px 20px rgba(0, 0, 0, 0.07);
    border-radius: 10px;
    border: solid 1px #eeeff1;
    background-color: #fff;

    @media screen and (max-width: 1059px) {
      box-shadow: unset;
      border-radius: unset;
      border: unset;
      background-color: inherit;
    }
  }

  &__body-header {
    display: flex;
    padding: 10px;
    align-items: center;
    justify-content: flex-end;

    @media screen and (max-width: 1059px) {
      display: none;
    }
  }

  &__body-header-content {
    display: flex;
    align-items: center;
    gap: 17px;

    span {
      color: #a6b7d4;
      font-size: 12px;
    }

    button {
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 0 3px 0 0;
      cursor: pointer;
    }
  }

  table {
    width: 100%;
    border-collapse: collapse;
    border: none;
    text-align: left;
    background-color: #fff;
    overflow-x: hidden;

    @media screen and (max-width: 1059px) {
      display: flex;
    }

    thead {
      @media screen and (max-width: 1059px) {
        display: none;
      }

      tr {
        height: 44px;
      }

      th {
        border: 1px solid #eeeff1;
        cursor: pointer;
        padding: 11px 10px;
        overflow: hidden;

        span {
          position: relative;
          display: block;
          white-space: nowrap;
          font-weight: 600;
        }
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

    th,
    td {
      text-align: left;
    }

    tbody {
      @media screen and (max-width: 1059px) {
        flex: 1;
        display: flex;
        flex-direction: column;
        gap: 5px;
      }

      tr {
        @media screen and (max-width: 1059px) {
          display: flex;
          flex-direction: column;
          gap: 12px;
          padding: 12px 15px 25px;
          border-radius: 10px;
          box-shadow: 0 5px 20px 0 rgba(0, 0, 0, 0.07);
          border: solid 1px #eeeff1;
          background-color: #fff;
        }
      }

      button {
        cursor: pointer;
      }

      td {
        padding: 5px 7px;
        position: relative;
        max-width: 46px;
        white-space: nowrap;
        text-overflow: clip;

        @media screen and (max-width: 1059px) {
          padding: 0;
          max-width: unset;
        }
      }
    }
  }

  .table {
    .active {
      border: dashed 2px #a6b7d4;
    }

    .rotate {
      width: 5px;
      height: 5px;
      transform: rotate(-90deg);
    }

    &__buttons {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin: auto;

      @media screen and (max-width: 1059px) {
        flex-direction: column;
        align-items: flex-start;
      }
    }

    &__button-col {
      width: 100%;

      @media screen and (max-width: 1059px) {
        width: auto;
        height: auto;
      }

      svg {
        pointer-events: none;
      }

      span {
        pointer-events: none;
        margin: 0 0 0 5px;
      }
    }

    &__action-buttons {
      position: relative;
      display: flex;
      justify-content: center;
      align-items: center;

      @media screen and (max-width: 1059px) {
        align-items: flex-start;
        flex-direction: column;
      }
    }

    &__delete-button {
      position: absolute;
      top: 32px;
      left: 12px;
      width: 179px;
      padding: 5px 9px;
      border-radius: 5px;
      box-shadow:
        0 0 3px 0 #000,
        inset 0 1px 2px 0 rgba(255, 255, 255, 0.5);
      background-color: #fff;
      z-index: 2;
      font-size: 14px;
      text-align: left;
      color: #ae0a0a;

      @media screen and (max-width: 1059px) {
        top: 36px;
        left: 0;
      }
    }

    &__cell-content {
      position: relative;
      border-radius: 5px;
      border: solid 1px #ccc;
      background-color: #fff;
      padding: 8px 22px 8px;
      overflow: hidden;
    }

    &__cell-button {
      position: absolute;
      top: 0;
      right: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      width: 21px;
      height: 100%;
      border-radius: 0 4px 4px 0;
      background-color: #f6f5f3;
    }

    .title {
      margin: 0 0 2px;
      font-size: 10px;
      color: #8f8f8f;
      display: none;

      @media screen and (max-width: 1059px) {
        display: block;
      }
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
    padding: 0 !important;
    margin: 0 !important;
    opacity: 1 !important;
    white-space: nowrap;
    text-overflow: clip;
    color: #000 !important;

    @media screen and (max-width: 1059px) {
      text-wrap: wrap;
    }
  }

  &__selected-options {
    padding: 0 !important;
    flex-wrap: nowrap !important;
    white-space: nowrap;
    overflow: hidden;
  }

  &__search {
    padding: 0 !important;
    margin: 0 !important;
  }

  &__open-indicator {
    display: none;
  }

  &__actions {
    padding: 0 !important;
    position: absolute;
    top: 1px;
    right: 1px;
    justify-content: center;
    width: 21px;
    height: 95%;
    border-radius: 0 4px 4px 0;
    background-color: #f6f5f3;
  }

  &__dropdown-toggle {
    margin: 0 !important;
    padding: 5px 20px !important;
    border-radius: 5px !important;
    border: solid 1px #ccc !important;
    background-color: #fff !important;

    @media screen and (max-width: 1059px) {
      padding: 10px 15px 9px !important;
    }
  }

  &__dropdown-menu {
    margin-top: 7px !important;
    border-radius: 5px !important;
    border: solid 1px #ccc !important;
    background-color: #fff !important;

    li {
      font-size: 14px;
      color: #161616;
    }
  }
}

.select-display .vs__selected {
  display: none;
}

#vs4__listbox {
  visibility: visible !important;
  display: block !important;
}
</style>
