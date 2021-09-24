<template>
  <q-page class="q-ma-xl">
    <div class="border-black relative-position" style="height:10vh">
      <div class="q-pa-md text-capitalize">{{ repoInfo.fullName }}</div>
      <div
        class="link q-pa-md q-px-xl q-mx-xl bg-white border-top-black"
        style="position:absolute; top:62px; height:8px"
      >
        issues
      </div>
    </div>
    <div class="row q-my-xl">
      <div class="col-8 border-black"></div>
      <div class="col-4"></div>
    </div>
  </q-page>
</template>

<script>
import axios from "axios";
import { defineComponent } from "vue";
const apiUrl = "https://api.github.com/repos/angular/angular";
export default defineComponent({
  name: "issuePage",
  data() {
    return {
      repoInfo: {},
      issue: {
        id: "",
        url: ""
      }
    };
  },
  mounted() {
    this.issue.id = this.$route.params.id;
    this.issue.url = this.$route.params.url;
    this.getRepoInfo();
    this.getIssueInfo();
  },
  methods: {
    getRepoInfo() {
      axios.get(apiUrl).then(response => {
        const data = response.data;
        this.repoInfo = {
          fullName: data.full_name,
          openIssues: data.open_issues
        };
      });
    },
    getIssueInfo() {
      axios.get(this.issue.id).then(response => {
        console.log(response);
      });
    }
  }
});
</script>
<style lang="scss" scoped>
.border-black {
  border: 2px solid black;
}
.border-top-black {
  border-top: 2px solid black;
  border-left: 2px solid black;
  border-right: 2px solid black;
}
</style>
