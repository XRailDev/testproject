<template>
  <div id="app">
    <div class="modal__box" v-show="addModalBox">
      <div class="modal__container">
        <div class="create__person">
          <h1 class="modal__title">Создание сотрудника</h1>
          <form class="form__data">
            <div class="wrapper">
              <p v-on:click="closeModalBox" class="link__back">
                Назад к списку
              </p>
              <input
                type="text"
                v-model="personName"
                @keyup.enter="addPerson"
                class="input"
                placeholder="Введите имя сотрудника"
                required
              />
              <input
                type="text"
                v-model="personLastName"
                @keyup.enter="addPerson"
                class="input"
                placeholder="Введите фамилию сотрудника"
                required
              />
              <div>
                <button
                  class="button__button button_type_modal"
                  type="submit"
                  v-on:click="addPerson"
                >
                  Сохранить
                </button>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>
    <div class="modal__box" v-show="updateModalBox">
      <div class="modal__container">
        <div class="create__person">
          <h1 class="modal__title">Редактирование сотрудника</h1>
          <form class="form__data">
            <div class="wrapper">
              <p v-on:click="closeModalBox" class="link__back">
                Назад к списку
              </p>
              <input
                type="text"
                v-model="updatePersonName"
                @keyup.enter="updatePerson"
                class="input"
                placeholder="Введите имя сотрудника"
                required
              />
              <input
                type="text"
                v-model="updatePersonLastName"
                @keyup.enter="updatePerson"
                class="input"
                placeholder="Введите фамилию сотрудника"
                required
              />
              <div>
                <button
                  class="button__button button_type_modal"
                  type="button"
                  v-on:click="updatePerson"
                >
                  Сохранить
                </button>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>
    <div class="persons__table">
      <table>
        <tr class="table__headers">
          <th>Имя</th>
          <th>Фамилия</th>
          <th></th>
          <th></th>
        </tr>
        <tr v-for="person of persons" :key="person.id">
          <td class="table__names">
            <img src="./assets/icon.png" class="img__person" alt="image" />
            {{ person.first_name }}
          </td>
          <td>{{ person.last_name }}</td>
          <td></td>
          <td class="buttons">
            <button
              v-on:click="showModalUpdate(person.id)"
              class="button__update"
            >
              <i class="fas fa-pencil-alt"></i>
            </button>

            <button v-on:click="deletePerson(person.id)" class="button__delete">
              <i class="fas fa-times"></i>
            </button>
          </td>
        </tr>
      </table>
    </div>
    <button class="button__button" type="button" v-on:click="showModal">
      Добавить сотрудника
    </button>
  </div>
</template>

<style>
@import "./styles/style.css";
</style>
<script>
import axios from "axios";
const baseURL = "http://localhost:3000/persons";
export default {
  name: "app",
  data() {
    return {
      persons: [],
      personName: "",
      personLastName: "",
      updatePersonName: "",
      updatePersonLastName: "",
      addModalBox: false,
      updateModalBox: false,
      user: 0
    };
  },
  async created() {
    try {
      const res = await axios.get("http://localhost:3000/persons");

      this.persons = res.data;
    } catch (e) {
      console.error(e);
    }
  },
  methods: {
    showModal() {
      this.addModalBox = true;
    },
    showModalUpdate(id) {
      this.updateModalBox = true;
      this.user = id;
    },
    closeModalBox() {
      this.addModalBox = false;
      this.updateModalBox = false;
    },
    async addPerson() {
      const res = await axios.post(baseURL, {
        first_name: this.personName,
        last_name: this.personLastName
      });

      this.persons = [...this.persons, res.data];

      (this.personName = ""), (this.personLastName = "");
      this.addModalBox = false;
    },
    updatePerson() {
      axios
        .put("http://localhost:3000/persons/" + this.user, {
          first_name: this.updatePersonName,
          last_name: this.updatePersonLastName
        }).then(
          response => {
            axios.get("http://localhost:3000/persons").then(response => {
              this.persons = response.data;
            });
          },
          reject => {
            console.log(reject);
          }
        );
      (this.updatePersonName = ""), (this.updatePersonLastName = "");
      this.updateModalBox = false;
    },
    deletePerson(id) {
      axios.delete("http://localhost:3000/persons/" + id).then(
        response => {
          axios.get("http://localhost:3000/persons").then(response => {
            this.persons = response.data;
          });
        },
        reject => {
          console.log(reject);
        }
      );
    }
  }
};
</script>
