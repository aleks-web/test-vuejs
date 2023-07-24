<template>
  <nav>
    <router-link v-if="isSaved" to="/">Главная</router-link>
    <router-link to="/test">Тестовое задание</router-link>
  </nav>
  <router-view />
</template>

<script>
export default {
  data() {
    return {
      isSaved: true, // Сохранены данные или нет
    };
  },
};
</script>

<style lang="scss">
// Решил не трогать, оставил как есть :)
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  box-sizing: border-box;
}

// Контейнер
.container {
  max-width: 1200px;
  margin: 0 auto;
}
// END Контейнер

// Блок с заголовком
.block-title {
  font-size: 25px;
}
// END Блок с заголовком

// Группа кнопок
.btn-group {
  display: flex;
  align-items: center;
  margin-bottom: 5px;
  gap: 10px;
  justify-content: space-between;
}
// END Группа кнопок

// Кнопка
.btn {
  padding: 10px;
  border: none;
  background-color: #42b983;
  color: white;
  font-weight: bold;
  transition: 0.3s;
  border-radius: 5px;
  font-size: 15px;
  min-width: max-content;
  width: 100%;
  cursor: pointer;

  &:hover {
    color: #42b983;
    background-color: #2c3e50;
  }

  &-clear {
    background-color: red;
  }
}
// END Кнопка

// Навигация
nav {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 14px;
  font-size: 20px;
  padding: 30px;

  a {
    font-weight: normal;
    color: #2c3e50;
    text-decoration: none;
    padding: 10px;
    border-radius: 5px;
    border: 3px solid transparent;

    &.router-link-exact-active {
      color: #42b983;
      border: 3px solid #42b983;
      font-weight: bold;
    }
  }
}
// END Навигация
</style>
