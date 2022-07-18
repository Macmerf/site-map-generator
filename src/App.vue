<template class="container">
  <div class="row justify-content-center">
    <div class="col-10 col-md-6 d-flex flex-column align-items-center">
      <h1 class="text-center my-4">Генератор sitemap</h1>
      <form class="w-100 d-flex flex-column align-items-center" @submit.prevent="sendSiteUrl" v-if="status == 'start'">
        <custom-input
          type="url"
          placeholder="https://example.com"
          required
          class="form-control p-2"
          v-model.trim="siteUrl"
        />
        <button
          type="submit"
          class="btn btn-outline-dark my-3 py-2 col-8 col-md-4"
        >
          Создать
        </button>
      </form>
      <status-bar
        v-else-if="status == 'loading'"
        :current="current"
        :totalCount="totalCount"
      />
      <slot v-else-if="status == 'ready'">
        <a
          :href="loadLink"
          class="btn btn-outline-dark my-3 py-2 col-8 col-md-4"
        >
          Скачать
        </a>
        <button
          @click="()=>{status = 'start'}"
          class="btn btn-outline-dark my-3 py-2 col-8 col-md-4"
        >
          На главную
        </button>
      </slot>
    </div>
  </div>
</template>

<script>
import CustomInput from "./components/CustomInput.vue";
import axios from "axios";
import StatusBar from "./components/StatusBar.vue";
export default {
  name: "App",
  components: { CustomInput, StatusBar },
  data() {
    return {
      siteUrl: "",
      taskStatus: "",
      loadLink: "",
      current: 0,
      totalCount: 0,
      status: "start",
    };
  },
  methods: {
    sendSiteUrl() {
      axios
        .post("https://api.sitemap-generator.ru/task", {
          origin: this.siteUrl,
          email: "",
        })
        .then((response) => {
          setTimeout(() => {
            this.checkStatus(response.data.id);
          }, 100);
          this.status = "loading";
        })
        .catch(function (error) {
          alert(error);
        });
    },
    checkStatus(taskId) {
      axios
        .get(`https://api.sitemap-generator.ru/task/stats/${taskId}`)
        .then((response) => {
          if (response.data.finished == undefined) {
            this.current = response.data.current;
            this.totalCount = response.data.totalCount;
            setTimeout(() => {
              this.checkStatus(taskId);
            }, 400);
          } else {
            this.taskStatus = response.data.finished;
            this.createDownloadLink(taskId);
          }
        })
        .catch(function (error) {
          alert(error);
        });
    },
    createDownloadLink(taskId) {
      axios
        .get(`https://api.sitemap-generator.ru/task/${taskId}`)
        .then((resp) => {
          this.loadLink = `https://api.sitemap-generator.ru/sitemap/${resp.data.sitemap}`;
          this.siteUrl = "";
          this.status = "ready";
        })
        .catch(function (error) {
          alert(error);
        });
    },
  },
};
</script>
