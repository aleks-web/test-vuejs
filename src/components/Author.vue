<template>
  <div class="author">
    <div class="author__img">
      <img alt="Vue logo" src="../assets/author.jpg" />
    </div>
    <h1>{{ name }}</h1>
    <div class="author__container container">
      <p>Мой телеграм: <a href="https://t.me/webalexey" target="_blanck">t.me/webalexey</a> - лучше написать сюда</p>
      <p>Мой номер телефона: +7 919 579 88 71 - не всегда могу ответить</p>
      <p>Резюме - <a href="https://disk.yandex.ru/i/jVljLq_nDbPb2A" target="_blanck">Мое резюме</a></p>
      <blockquote>
        <p>Одна из последних работ - <a href="https://universal-service.uplinkweb.ru/" target="_blanck">universal-service.uplinkweb.ru</a></p>
        <p>Задача: создать универсальный сайт под разные услуги. Заточить под SEO</p>
        <p>Что использовал: верстка по БЭМ, макет из Figma, SCSS, Js, JavaScript, JQuery, PHP, Gulp, натяжка на CMS MODX</p>
      </blockquote>
    </div>
  </div>
</template>

<script>
export default {
  name: "Author",
  props: {
    name: String,
  },
};
</script>

<style lang="scss" scoped>
// Цитата
blockquote {
  padding: 20px 50px;
  background-color: #b1ffdc;
  width: max-content;
  border-radius: 5px;
}
// END Цитата

.author {
  &__container {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  &__img {
    width: 20%;
    height: 20vw;
    overflow: hidden;
    margin: 0 auto;
    border-radius: 100%;

    img {
      width: 100%;
      object-fit: cover;
    }
  }
}
a {
  color: #42b983;
}
</style>
