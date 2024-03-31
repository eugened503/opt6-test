<template>
  <div class="basket">
    <div class="basket__body">
      <ul>
        <li>
          <p>Сумма:</p>
          <p>152 212 руб</p>
        </li>
        <li>
          <p>Кол-во:</p>
          <p>24 шт</p>
        </li>
        <li>
          <p>Общий вес:</p>
          <p>2 322 кг</p>
        </li>
      </ul>
    </div>
    <div class="basket__footer">
      <p>Общая сумма:</p>
      <p>152 212 руб</p>
    </div>
  </div>
</template>

<script setup></script>

<style scoped lang="scss">
.basket {
  display: flex;
  flex-direction: column;
  gap: 5px;
  max-width: 304px;
  margin: 15px 10px 0 auto;

  @media screen and (max-width: 1059px) {
    margin: 15px 0 0 auto;
  }

  &__body,
  &__footer {
    padding: 15px;
    border-radius: 5px;
    border: solid 1px #eeeff1;
    background-color: #fbfcfd;
  }

  ul {
    display: flex;
    flex-direction: column;
    gap: 15px;
  }

  li {
    display: flex;
    justify-content: space-between;
    color: #000;
  }

  li p {
    font-size: 14px;

    @media screen and (max-width: 1059px) {
      font-size: 16px;
    }

    &:first-child {
      color: #8f8f8f;
    }
  }

  &__footer {
    display: flex;
    justify-content: space-between;
  }

  &__footer p {
    &:nth-child(2) {
      font-weight: 600;
    }
  }
}
</style>
