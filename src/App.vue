<template>
  <div v-if="step === 0" class="text-center pt-2 100vh 100vw">
    <h5>Имя: {{ userInfo.userName }}</h5>
    <h5>Почта: {{ userInfo.userEmail }}</h5>
    <div class="container mt-5">
      <div class="row row-cols-2">
        <div class="left-side col-3">
          <h4>Папки</h4>
          <button
            @click="showDialog"
            class="btn btn-outline-success btn-lg"
          >
            Создать
          </button>
          <my-dialog v-model:show="dialogVisible">
            <folder-form @create="createFolder"/>
          </my-dialog>
          <my-dialog v-model:show="dialogEditVisible">
            <folder-form-edit @edit="editFolderForm" />
          </my-dialog>
          <folder-list 
            :folders="folders"
            @remove="removeFolder"
            @edit="editFolder"
            @showNotes="showNotes"
          />
        </div>
        <div class="right-side col-9 text-center">
          <h4>Заметки</h4>
          <button
            v-if="selectedFolderId.length > 0"
            @click="showNoteDialog"
            class="btn btn-outline-success btn-lg"
          >
            Создать
          </button>
          <my-dialog v-model:show="dialogNoteVisible">
            <note-form-create @create="createNote" />
          </my-dialog>
          <my-dialog v-model:show="dialogEditNoteVisible">
            <note-form-edit @edit="editNoteForm" />
          </my-dialog>
          <div class="notes">
            <notes-list
              :notes="notes"
              @edit="editNote"
              @remove="removeNote"
              class="d-flex flex-wrap"
            />
          </div>
        </div>
      </div>
    </div>
  </div>

  <div
    v-else
    class="container d-flex justify-content-center align-items-center vh-100 vw-100"
  >
    <form
      @submit.prevent="autorizationUser"
      novalidate
      v-show="step === 1"
      class="border p-5 rounded"
    >
      <div class="step">
        <div class="mb-3">
          <label for="name" class="form-label">Имя</label>
          <input
            v-model="state.formAutoriz.name"
            type="text"
            class="form-control"
            id="name"
          />
        </div>
        <div class="mb-3">
          <label for="password" class="form-label">Пароль</label>
          <input
            v-model="state.formAutoriz.password"
            type="password"
            class="form-control"
            id="password"
          />
        </div>
        <button type="submit" class="btn btn-primary mb-1">Войти</button>
        <p>
          Нет аккаунта?
          <span @click="register" role="button" class="text-primary"
            >Зарегистрироваться</span
          >
        </p>
      </div>
    </form>

    <form
      @submit.prevent="registrationUser"
      novalidate
      v-show="step === 2"
      class="border p-5 rounded"
    >
      <div class="step">
        <div class="mb-3">
          <label for="name" class="form-label">Имя</label>
          <input
            v-model="state.formReg.name"
            type="text"
            class="form-control"
            id="name"
          />
        </div>
        <div class="mb-3">
          <label for="email" class="form-label">Почта</label>
          <input
            v-model="state.formReg.email"
            type="email"
            class="form-control"
            id="email"
          />
        </div>
        <div class="mb-3">
          <label for="password" class="form-label">Пароль</label>
          <input
            v-model="state.formReg.password"
            type="password"
            class="form-control"
            id="password"
          />
        </div>
        <div class="mb-3">
          <label for="confirmPassword" class="form-label"
            >Повторить пароль</label
          >
          <input
            v-model="state.formReg.confirmPassword"
            type="password"
            class="form-control"
            id="confirmPassword"
          />
        </div>
        <button type="submit" class="btn btn-primary mb-1">
          Зарегистрироваться
        </button>
        <p>
          У меня уже есть аккаунт.
          <span @click="autorization" role="button" class="text-primary"
            >Войти</span
          >
        </p>
      </div>
    </form>
  </div>
</template>

<script>
import FolderForm from "@/components/FolderForm.vue";
import FolderList from "@/components/FolderList.vue";
import { useVuelidate } from "@vuelidate/core";
import {
  required,
  email,
  minLength,
  maxLength,
  sameAs,
} from "@vuelidate/validators";
import { reactive, computed } from "vue";
import axios from "axios";
import FolderFormEdit from "./components/FolderFormEdit.vue";
import NotesList from "./components/NotesList.vue";
import NoteFormCreate from "./components/NoteFormCreate.vue";
import NoteFormEdit from "./components/NoteFormEdit.vue";


export default {
  setup() {
    const state = reactive({
      formAutoriz: {
        name: "",
        password: "",
      },
      formReg: {
        name: "",
        email: "",
        password: "",
        confirmPassword: "",
      },
    });
    const rules = computed(() => {
      return {
        formAutoriz: {
          name: { required },
          password: { required },
        },
        formReg: {
          name: { required, maxLength: maxLength(32) },
          email: { required, email },
          password: {
            required,
            minLength: minLength(6),
            maxLength: maxLength(32),
          },
          confirmPassword: { required, sameAs: sameAs(state.formReg.password) },
        },
      };
    });
    const v$ = useVuelidate(rules, state);
    return {
      state,
      v$,
    };
  },
  data() {
    return {
      step: 1,
      userInfo: {},
      folders: [],
      notes: [],
      dialogVisible: false,
      dialogEditVisible: false,
      dialogNoteVisible: false,
      dialogEditNoteVisible: false,
      selectedFolderEditId: '',
      selectedFolderId: '',
      selectedNoteEditId: '',
    };
  },
  mounted() {
    if (localStorage.accessToken) {
      this.fetchUserInfo();
      this.step = 0;
      this.fetchFolders();
      axios
        .post("https://test-api.misaka.net.ru/api/Account/login", {
          username: localStorage.userName,
          password: localStorage.userPassword,
        })
        .then(function (response) {
          localStorage.accessToken = response.data.accessToken;
          localStorage.refreshToken = response.data.refreshToken;
        })
        .catch(() => {
          console.log("Пользователь не найден");
        });
      setInterval(() => {
        axios
          .post("https://test-api.misaka.net.ru/api/Account/refresh-token", {
            refreshToken: localStorage.refreshToken,
          })
          .then(function (response) {
            localStorage.accessToken = response.data.accessToken;
            localStorage.refreshToken = response.data.refreshToken;
          })
          .catch(() => {
            console.log("Токен не загрузился");
          });
      }, 900000);
    }
  },
  methods: {
    register() {
      if (this.step === 1) {
        this.step++;
      }
    },
    autorization() {
      if (this.step === 2) {
        this.step--;
      }
    },
    autorizationUser() {
      this.v$.$validate();
      if (!this.v$.formAutoriz.$error) {
        const user = {
          name: this.state.formAutoriz.name,
          password: this.state.formAutoriz.password,
        };
        axios
          .post("https://test-api.misaka.net.ru/api/Account/login", {
            username: this.state.formAutoriz.name,
            password: this.state.formAutoriz.password,
          })
          .then(function (response) {
            localStorage.accessToken = response.data.accessToken;
            localStorage.refreshToken = response.data.refreshToken;
            localStorage.userName = user.name;
            localStorage.userPassword = user.password;
            location.reload();
            this.step = 0;
          })
          .catch(() => {
            console.log("Пользователь не найден");
          });
      } else {
        console.log("Авторизация прошла неудачно");
      }
    },
    registrationUser() {
      this.v$.$validate();
      if (!this.v$.formReg.$error) {
        const user = {
          name: this.state.formReg.name,
          password: this.state.formReg.password,
        };
        axios
          .post("https://test-api.misaka.net.ru/api/Account/register", {
            username: this.state.formReg.name,
            email: this.state.formReg.email,
            password: this.state.formReg.password,
          })
          .then(function (response) {
            localStorage.accessToken = response.data.accessToken;
            localStorage.refreshToken = response.data.refreshToken;
            localStorage.userName = user.name;
            localStorage.userPassword = user.password;
            location.reload();
            this.step = 0;
          })
          .catch(() => {
            console.log("Пользователь зарегистрирован");
          });
      } else {
        console.log("Регистрация прошла неудачно");
      }
    },
    async fetchUserInfo() {
      try {
        const response = await
        axios
        .get("https://test-api.misaka.net.ru/api/Account/user", {
          headers: {
            Authorization: `Bearer ${localStorage.accessToken}`,
          },
        });
        this.userInfo.userName = response.data.username;
        this.userInfo.userEmail = response.data.email;
      } catch (e) {
        console.log("Невозможно загрузить информацию о пользователе");
      }
    },
    createFolder(folder) {
      axios
        .post("https://test-api.misaka.net.ru/api/Folders", {
          name: folder.name,
          color: folder.color
        },
        {
          headers: {
            Authorization: `Bearer ${localStorage.accessToken}`,
          }
        })
        .then(() => {
          this.fetchFolders()
        })
        .catch(() => {
          console.log("Ошибка на добавление папки");
        });
      this.dialogVisible = false;
    },
    removeFolder(folder) {
      axios
      .delete(`https://test-api.misaka.net.ru/api/Folders/${folder.id}`, {
        headers: {
          Authorization: `Bearer ${localStorage.accessToken}`,
        }
      })
      .then(() => {
        this.fetchFolders();
      })
      .catch(() => {
        console.log('Папка не удалена')
      })
    },
    editFolder(folder) {
      this.dialogEditVisible = true;
      this.selectedFolderEditId = folder.id
    },
    editFolderForm(folder) {
      axios
      .put(`https://test-api.misaka.net.ru/api/Folders/${this.selectedFolderEditId}`,
      {
        name: folder.name,
        color: folder.color
      },
      {
        headers: {
          Authorization: `Bearer ${localStorage.accessToken}`,
        }
      })
      .then(() => {
        this.fetchFolders();
        this.dialogEditVisible = false;
      })
      .catch(() => {
        console.log('Папка не изменилась')
      })
    },
    createNote(note) {
      axios
        .post(`https://test-api.misaka.net.ru/api/Folders/${this.selectedFolderId}/notes`, {
          title: note.title,
          content: note.content,
          color: note.color
        },
        {
          headers: {
            Authorization: `Bearer ${localStorage.accessToken}`,
          }
        })
        .then(() => {
          this.fetchNotes();
          this.fetchFolders();
          this.dialogNoteVisible = false;
        })
        .catch(() => {
          console.log("Ошибка на добавление заметки");
        });
      this.dialogVisible = false;
    },
    removeNote(note) {
      axios
      .delete(`https://test-api.misaka.net.ru/api/Notes/${note.id}`, {
        headers: {
          Authorization: `Bearer ${localStorage.accessToken}`,
        }
      })
      .then(() => {
        this.fetchNotes();
        this.fetchFolders();
      })
      .catch(() => {
        console.log('Заметка не удалена');
      })
    },
    editNote(note) {
      this.dialogEditNoteVisible = true;
      this.selectedNoteEditId = note.id;
    },
    editNoteForm(note) {
      axios
      .put(`https://test-api.misaka.net.ru/api/Notes/${this.selectedNoteEditId}`,
      {
        title: note.title,
        content: note.content,
        color: note.color
      },
      {
        headers: {
          Authorization: `Bearer ${localStorage.accessToken}`,
        }
      })
      .then(() => {
        this.fetchNotes();
        this.dialogEditNoteVisible = false;
      })
      .catch(() => {
        console.log('Заметка не изменилась')
      })
    },
    showNotes(folder) {
      this.selectedFolderId = folder.id;
      this.fetchNotes();
    },
    showDialog() {
      this.dialogVisible = true;
    },
    showNoteDialog() {
      this.dialogNoteVisible = true;
    },
    async fetchFolders() {
      try {
        const response = await
        axios.get("https://test-api.misaka.net.ru/api/Folders", {
          headers: {
            Authorization: `Bearer ${localStorage.accessToken}`,
          },
        });
        this.folders = response.data;
      } catch (e) {
        console.log("Ошибка получения папок");
      }
    },
    async fetchNotes() {
      try {
        const response = await
        axios.get(`https://test-api.misaka.net.ru/api/Folders/${this.selectedFolderId}/notes`, {
          headers: {
            Authorization: `Bearer ${localStorage.accessToken}`,
          },
        });
        this.notes = response.data;
      } catch (e) {
      console.log("Ошибка получения заметок");
      }
    }
  },
  components: { FolderForm, FolderList, FolderFormEdit, NotesList, NoteFormCreate, NoteFormEdit },
};
</script>

<style></style>
