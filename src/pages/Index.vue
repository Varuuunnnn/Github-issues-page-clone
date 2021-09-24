<template>
  <q-page class="q-ma-xl">
    <div class="border-black relative-position" style="height:10vh">
      <div class="q-pa-md">{{ repoInfo.fullName }}</div>
      <div
        class="link q-pa-md q-px-xl q-mx-xl bg-white border-top-black"
        style="position:absolute; top:61px; height:8px"
      >
        issues
      </div>
    </div>

    <div>
      <q-input
        style="width:50%"
        class="bg-white q-my-lg"
        placeholder="Search text"
        outlined
        v-model="searchText"
        @keydown.enter.prevent="searchIssue()"
      >
      </q-input>
      <q-markup-table>
        <thead class="bg-grey-4">
          <tr>
            <th class="text-left">{{ repoInfo.openIssues }}{{ " " }}Open</th>
            <th class="text-left">{{ closedIssuesCount }}{{ " " }} Closed</th>
            <th class="text-left">Labels Filter</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(issue, i) in filteredIssues" :key="i">
            <td
              class="text-left"
              @click="
                $router.push({
                  name: 'issue-page',
                  params: { id: issue.id, url: issue.url }
                })
              "
            >
              {{ issue.title }}
            </td>
            <td class="text-left">
              <div v-for="(labels, i) in issue.labels" :key="i">
                <q-chip color="blue" :label="labels.name" text-color="white" />
              </div>
            </td>
          </tr>
        </tbody>
      </q-markup-table>
      <div class="flex flex-center items-center q-my-lg">
        <div
          class="cursor-pointer"
          v-if="currentPage >= 1"
          @click="togglePagePrev"
        >
          Previous
        </div>
        <q-pagination class="q-mx-lg" v-model="currentPage" :max="3" />
        <div class="cursor-pointer" @click="togglePageNext">
          Next
        </div>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from "vue";
import axios from "axios";
const apiUrl = "https://api.github.com/repos/angular/angular";
const apiUrlForGettingClosedIssues =
  "https://api.github.com/search/issues?q=repo:angular/angular/node+type:issue+state:open&per_page=10&page=1";

export default defineComponent({
  name: "PageIndex",
  data() {
    return {
      searchText: "",
      repoInfo: {},
      closedIssuesCount: "",
      perPafe: 10,
      currentPage: 1,
      issues: [],
      filteredIssues: []
    };
  },
  mounted() {
    this.getRepoInfo();
    this.getIssuesList(1);
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
    async getIssuesList(page) {
      this.issues = [];
      this.filteredIssues = [];
      await axios
        .get(
          `https://api.github.com/search/issues?q=repo:angular/angular/node+type:issue+state:open&per_page=10&page=${page}`
        )
        .then(response => {
          const data = response.data;
          this.closedIssuesCount = data.total_count;
          const issues = data.items;
          issues.forEach(issue => {
            this.issues.push({
              id: issue.id,
              title: issue.title,
              url: issue.url,
              labels: issue.labels
            });
          });
        });
      this.filteredIssues = this.issues;
    },

    searchIssue() {
      this.filteredIssues = this.issues.filter(val => {
        return val.title.toLowerCase().includes(this.searchText.toLowerCase());
      });
    },
    togglePageNext() {
      const page = this.currentPage + 1;
      this.getIssuesList(page);
    },
    togglePagePrev() {
      const page = this.currentPage - 1;

      this.getIssuesList(page);
    }
  },
  watch: {
    searchText() {
      if (this.searchText.length === 0) {
        this.filteredIssues = this.issues;
      }
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
