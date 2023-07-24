<template>
  <div class="page-test">
    <div class="block-title">
      <h1>Тестовое задание</h1>
    </div>

    <main class="main-content">
      <div class="main-content__container container">
        <aside class="sidebar">
          <span class="sidebar__player-count">
            <span class="sidebar__player-count-title">Общее количество игроков</span>
            <strong class="sidebar__player-count-num">{{ players.length }}</strong>
          </span>
          <span class="sidebar__input">
            <span class="sidebar__input-label">Допустимое количество игроков в группе</span>
            <input type="number" v-model="maxPlayersInGroup" />
          </span>
          <div class="btn-group">
            <button class="btn" @click="randomResult()">Рандом</button>
            <button class="btn" @click="consoleResult()" v-if="this.hasFullGroups()">Сохранить</button>
            <button class="btn btn-clear" @click="clearResult()">Очистить группы</button>
          </div>
          <div class="sidebar__wrap">
            <div class="sidebar__item">
              <span class="sidebar__text">ФИО</span>
              <span class="sidebar__text">Дата рождения</span>
            </div>

            <div
              class="sidebar__item"
              v-for="player in this.sortPlayers.filter((item) => this.getGroupPlayerById(item.id) == 0)"
              :key="player.id"
              draggable="true"
              @dragstart="onDragstart($event, player)"
            >
              <span class="sidebar__text sidebar__text-name" @dblclick="addPlayerInGroup(player.id, this.nowGroup)">{{ getFullName(player.id) }}</span>
              <span class="sidebar__text">{{ player.birthday }}</span>
            </div>
          </div>
        </aside>

        <div class="content-groups">
          <div
            class="content-groups__group"
            @click="setActiveGroup(group.group_id)"
            @dragover.prevent
            @dragenter.prevent=""
            @drop="onDrop($event, group)"
            v-for="group in groups"
            :key="group.group_id"
            :class="classGroup(group.group_id)"
          >
            <span class="content-groups__group-title">{{ group.title }}</span>
            <div class="content-groups__group-list">
              <span class="content-groups__group-none" v-if="!getPleyersCountInGroup(group.group_id)">Перетащите игроков в группу</span>
              <span
                class="content-groups__group-item"
                @click="deletePlayerInGroup(getObjectPlayer(res.player_id))"
                v-for="res in result.filter((item) => item.group_id == group.group_id)"
                :key="res.player_id"
                >{{ getFullName(res.player_id) }}</span
              >
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // Максимальное количество игроков в группе
      maxPlayersInGroup: 3,
      //Текущая группа
      nowGroup: 1,
      // Сохранен ли результат

      /**
       * Игроки
       */
      players: [
        {
          id: 164679,
          name: "Руслан",
          surname: "Иванов",
          birthday: "2009-07-07",
        },
        {
          id: 164680,
          name: "Григорий",
          surname: "Корнилов",
          birthday: "2005-04-03",
        },
        {
          id: 163246,
          name: "Артем",
          surname: "Пулов",
          birthday: "1998-08-09",
        },
        {
          id: 164535,
          name: "Иван",
          surname: "Путров",
          birthday: "2007-04-10",
        },
        {
          id: 163604,
          name: "Александр",
          surname: "Назаров",
          birthday: "2004-10-06",
        },
        {
          id: 163636,
          name: "Иван",
          surname: "Киселев",
          birthday: "2005-10-29",
        },
        {
          id: 164836,
          name: "Ольгерд",
          surname: "Ковенко",
          birthday: "2006-12-25",
        },
        {
          id: 164101,
          name: "Павел",
          surname: "Кондратьев",
          birthday: "1985-10-20",
        },
        {
          id: 164457,
          name: "Иван",
          surname: "Ковенко",
          birthday: "2006-07-18",
        },
        {
          id: 162937,
          name: "Александр",
          surname: "Миронов",
          birthday: "2000-03-29",
        },
        {
          id: 164009,
          name: "Артем",
          surname: "Красковский",
          birthday: "1985-10-20",
        },
      ],

      /**
       * Группы (решил приукрасить немного массив)
       */
      groups: [
        {
          group_id: 1,
          title: "Группа 1",
          activeClass: true,
          disableClass: false,
        },
        {
          group_id: 2,
          title: "Группа 2",
          activeClass: false,
          disableClass: false,
        },
        {
          group_id: 3,
          title: "Группа 3",
          activeClass: false,
          disableClass: false,
        },
      ],

      result: [],
    };
  },

  methods: {
    /**
     * Возвращает объект игрока по его id
     *
     * @param {Number} playerId - id игрока
     * @returns {Object} arr - возвращает объект игрока
     */
    getObjectPlayer(playerId) {
      let arr = null;

      this.players.forEach((x) => {
        if (x.id == playerId) {
          arr = x;
        }
      });

      return arr;
    },
    // END Возвращаем объект игрока по id

    /**
     * Возвращает индекс группы в массиве
     *
     * @param {Number} groupId - id игрока
     * @returns {Number} возвращает индекс группы
     */
    getGroupIndexKey(groupId) {
      let index = null;

      this.groups.forEach((group, i) => {
        if (groupId == group.group_id) {
          index = i;
        }
      });

      return index;
    },
    // END Возвращает индекс группы в массиве

    /**
     * Задает активную группу по id группы
     *
     * @param {Number} groupId - id игрока
     */
    setActiveGroup(groupId) {
      this.nowGroup = groupId;

      this.groups.forEach((x) => {
        if (x.group_id == groupId) {
          x.activeClass = true;
        } else {
          x.activeClass = false;
        }
      });
    },
    // END Задает активную группу по id группы

    /**
     * Убирает активную группу
     */
    unsetActiveGroup() {
      this.nowGroup = null;

      this.groups.forEach((x) => {
        x.activeClass = false;
      });
    },
    // END Убирает активную группу

    /**
     * Получает полное имя игрока
     *
     * @param {Number} playerId - id игрока
     * @returns {String} возврощает полное имя игрока
     */
    getFullName(playerId) {
      let player = this.getObjectPlayer(playerId);

      return `${player.name} ${player.surname}`;
    },

    /**
     * Удаляет игрока из группы
     *
     * @param {Object} player - Объект игрока
     */
    deletePlayerInGroup(player) {
      this.result.forEach((x) => {
        if (x.player_id == player.id) {
          this.nowGroup = x.group_id;
          x.group_id = "";
        }
      });

      this.$root.isSaved = false;
    },
    // END Удаляет игрока из группы

    /**
     * Добавляет игрока в группу
     *
     * @param {Number} playerId - id игрока
     * @param {Number} groupId - id группы
     */
    addPlayerInGroup(playerId, groupId) {
      if (this.getPleyersCountInGroup(groupId) < this.maxPlayersInGroup && this.groups.at(-1).group_id > groupId) {
        this.setActiveGroup(groupId + 1);
      }

      if (this.groups.at(-1).group_id == groupId) {
        this.setActiveGroup(groupId);
      }

      if (this.getGroupPlayerById(playerId) == groupId) {
        alert("Человек уже состоит в данной группе");
        return;
      }

      if (this.getPleyersCountInGroup(groupId) >= this.maxPlayersInGroup) {
        alert(`Максимальное количестов человек в каждой группе: ${this.maxPlayersInGroup}`);
        if (this.groups.at(-1).group_id > groupId) {
          this.setActiveGroup(groupId + 1);
        }
        return;
      }

      if (this.getPleyersCountInGroup(groupId) < this.maxPlayersInGroup && groupId && playerId) {
        let arr = {
          player_id: playerId,
          group_id: groupId,
        };

        this.result.push(arr);
      }

      this.$root.isSaved = false;
    },
    // END Добавляет игрока в группу

    /**
     * Механизм начала перетаскивания (drag) игрока в какую-либо группу
     *
     * @param {Object} event - Объект события
     * @param {Object} player - Объект игрока
     */
    onDragstart(event, player) {
      let playerId = Number(player.id);

      event.dataTransfer.dropEffect = "move";
      event.dataTransfer.effectAllowed = "move";
      event.dataTransfer.setData("playerId", playerId.toString());
    },
    // END Механизм начала перетаскивания (drag) игрока в какую-либо группу

    /**
     * Механизм дропа игрока в группу
     *
     * @param {Object} event - Объект события
     * @param {Object} group - Объект группы
     */
    onDrop(event, group) {
      let playerId = Number(event.dataTransfer.getData("playerId"));

      this.addPlayerInGroup(playerId, group.group_id);
    },
    // END Механизм дропа игрока в группу

    /**
     * Получить группу в которой состоит игрок по его id
     *
     * @param {Number} playerId - id игрока
     * @returns {Number} возврощает id группы, в которой находится игрок либо 0 (false)
     */
    getGroupPlayerById(playerId) {
      let groupId = null;

      this.result.forEach((x) => {
        if (playerId == x.player_id) {
          groupId = x.group_id;
        }
      });

      return Number(groupId);
    },
    // END Получить группу в которой состоит игрок по его id

    /**
     * Метод получающий количество человек в текущей группе
     *
     * @param {Number} groupId - id группы
     * @returns {Number} возвращает количество человек в группе
     */
    getPleyersCountInGroup(groupId) {
      let count = null;

      this.result.forEach((x) => {
        if (groupId == x.group_id) {
          count++;
        }
      });

      count = Number(count);

      return Number(count);
    },
    // END Метод получающий количество человек в текущей группе

    /**
     * Метод рандомного распределения игроков по группам
     */
    randomResult() {
      let customPlayers = this.players
        .map((i) => [Math.random(), i])
        .sort()
        .map((i) => i[1]); // Сортируем в рандомном порядке

      this.clearResult();

      this.groups.forEach((group, index) => {
        customPlayers.map((x, i, o) => {
          if (this.getPleyersCountInGroup(group.group_id) < this.maxPlayersInGroup) {
            this.addPlayerInGroup(x.id, group.group_id);
            delete o[i];
          }
        });
      });
    },
    // END Метод рандомного распределения игроков по группам

    /**
     * Очистка игроков из групп
     */
    clearResult() {
      if (this.result.length != 0) {
        this.$root.isSaved = false;
        this.result = [];
      }
      this.unsetActiveGroup();
      this.setActiveGroup(this.groups[0].group_id);
    },
    // END Очистка игроков из групп

    /**
     * Итоговый массив
     */
    consoleResult() {
      console.log("Итоговый массив для отправки на сервер:");
      console.log(this.result);
      this.$root.isSaved = true;
      this.$router.push({ name: "main" });
    },
    // END Очистка игроков из групп

    /**
     * Метод возвращает массив с классами для группы
     *
     * @param {Number} groupId - id группы
     * @returns {Object} возвращает объект с css классами
     */
    classGroup(groupId) {
      let index = this.getGroupIndexKey(groupId);

      return {
        "content-groups__group--active": this.groups[index].activeClass,
        "content-groups__group--disable": this.groups[index].disableClass,
      };
    },
    // END Метод возвращает массив с классами для группы

    /**
     * Метод проверки заполнения групп
     */
    hasFullGroups() {
      let hasFull = 0;
      let lengthPlayers = this.sortPlayers.filter((item) => this.getGroupPlayerById(item.id) == 0).length;

      this.groups.forEach((x, i) => {
        let count = this.getPleyersCountInGroup(x.group_id);

        if (count >= this.maxPlayersInGroup) {
          hasFull++;
        }
      });

      if (hasFull == this.groups.length || !lengthPlayers) {
        return true;
      } else {
        return false;
      }
    },

    // END Метод проверки заполнения групп
  },

  computed: {
    /**
     * Возвращаем отсортированный список по имени в алфавитном порядке
     */
    sortPlayers() {
      return this.players.sort((a, b) => {
        if (a.name > b.name) {
          return 1;
        } else {
          return -1;
        }
      });
    },
    // END Возвращаем отсортированный список по имени в алфавитном порядке
  },
};
</script>

<style lang="scss" scoped>
// Переменные scss
$color-1: #2c3e50;
$color-2: #42b983;
$color-3: #85dcb5;
$color-4: #ddd;
// END Переменные scss

// Группы
.content-groups {
  display: grid;
  grid-template-areas:
    ". ."
    "last last";
  width: 60%;
  gap: 3%;

  &__group {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    font-size: 20px;
    font-weight: bold;
    background-color: #42b983;
    border-radius: 10px;
    color: white;
    min-width: 300px;
    overflow: hidden;
    padding: 60px 0;
    transition: 0.2s all;
    cursor: pointer;

    &:hover {
      background-color: $color-3;
    }

    &:before {
      content: "Текущая группа";
      position: absolute;
      top: 10px;
      right: 10px;
      background-color: green;
      padding: 5px 10px;
      font-size: 15px;
      font-weight: normal;
      border-radius: 6px;
      transition: 0.2s;
      opacity: 0;
      visibility: hidden;
    }

    &--active {
      position: relative;
      background-color: $color-3;
      cursor: context-menu;

      &:before {
        opacity: 1;
        visibility: visible;
      }
    }

    &--disable {
      background-color: red !important;
    }

    &:last-child {
      grid-area: last;
    }

    &-title {
      margin-bottom: 30px;
      background-color: $color-1;
      padding: 10px;
      border-radius: 5px;
    }

    &-none {
      padding: 15px 10px;
      width: max-content;
      background-color: white;
      color: $color-1;
      border: 5px dashed $color-1;
      font-weight: normal;
    }

    &-list {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 5px;
      width: 100%;
    }

    &-item {
      background-color: $color-1;
      padding: 5px 10px;
      font-weight: normal;
      display: block;
      cursor: pointer;
      transition: 0.3s all;

      &:hover {
        background-color: $color-4;
        color: $color-1;
      }
    }
  }
}
// END Группы

// Блок с основным содержимым
.main-content {
  &__container {
    display: flex;
    justify-content: space-between;
    gap: 3%;
    margin-bottom: 100px;
  }
}
// END Блок с основным содержимым

// Оформление сайтбара с таблицей
.sidebar {
  border-radius: 5px;
  width: 35%;

  // Обертка
  &__wrap {
    display: flex;
    flex-direction: column;
    gap: 4px;
    border-radius: 2px;
  }

  // Счетчик игроков
  &__player-count {
    background-color: #2c3e50;
    color: white;
    padding: 10px;
    display: flex;
    margin-bottom: 3px;
    border-top-left-radius: 10px;
    border-top-right-radius: 10px;
    gap: 5px;
    align-items: center;
    justify-content: space-between;

    &-num {
      font-size: 25px;
    }
  }
  // END Счетчик игроков

  // Стилизация элемента с input
  &__input {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 10%;
    margin-bottom: 3px;
    background-color: $color-1;
    padding: 10px;
    color: white;

    &-label {
      width: 80%;
    }

    input {
      width: 10%;
      padding: 5px;
      text-align: center;
      border: none;
      border-radius: 5px;
      outline: none;
    }
  }
  // END Стилизация элемента с input

  // Пункт из таблицы
  &__item {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 10px;
    padding: 10px 10px 8px 10px;
    background-color: $color-4;
    transition: 0.3s all;
    cursor: all-scroll;

    &:hover {
      background-color: #2c3e50;
      color: white;

      .sidebar__text-name {
        background-color: #fff;
        color: $color-1;
      }
    }

    &:first-child {
      background-color: $color-2;
      color: white;
      font-weight: bold;
      cursor: context-menu;
    }
  }

  &__text {
    &-name {
      padding: 5px 10px;
      border-radius: 3px;
      cursor: pointer;
      width: max-content !important;
      transition: 0.2s all;

      &:hover {
        background-color: $color-2 !important;
        color: white !important;
      }
    }
    &:first-child {
      width: 40%;
      display: flex;
    }
    &:last-child {
      width: 40%;
      display: flex;
      justify-content: end;
    }
  }
}
// END Оформление сайтбара с таблицей
</style>
