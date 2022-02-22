<template>
  <div class="container">
    <!-- パンくずリスト -->
    <nav>
      <div class="nav-wrapper">
        <div class="col s12 teal">
          <a class="breadcrumb">従業員リスト</a>
        </div>
      </div>
    </nav>
    <form class="search">
      従業員名検索：<input type="text" v-model="name" />
      <button type="button" v-on:click="searchName(name)">検索</button>
      <div v-for="Name of searchName(name)" v-bind:key="Name.id"></div>
    </form>
    <div>従業員数:{{ getEmployeeCount }}人</div>
    <div class="row">
      <table class="striped">
        <thead>
          <tr>
            <th>名前</th>
            <th>入社日</th>
            <th>扶養人数</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="employee of currentEmployeeList" v-bind:key="employee.id">
            <td>
              <router-link :to="'/employeeDetail/' + employee.id">{{
                employee.name
              }}</router-link>
            </td>

            <td>{{ employee.formatHireDate }}</td>
            <td>{{ employee.dependentsCount }}人</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import { Employee } from "@/types/employee";
/**
 * 従業員一覧を表示する画面.
 */
@Component
export default class EmployeeList extends Vue {
  // 従業員一覧
  private currentEmployeeList: Array<Employee> = [];
  // 従業員数
  private employeeCount = 0;

  /**
   * Vuexストアのアクション経由で非同期でWebAPIから従業員一覧を取得する.
   *
   * @remarks
   * Vueインスタンスが生成されたタイミングで
   * Vuexストア内のアクションを呼ぶ(ディスパッチする)。
   * ライフサイクルフックのcreatedイベント利用。
   *
   * 取得してからゲットするため、async awaitを利用している。
   */
  async created(): Promise<void> {
    await this.$store.dispatch("getEmployeeList");

    // 従業員一覧情報をVuexストアから取得
    // 非同期で外部APIから取得しているので、async/await使わないとGetterで取得できない
    // ページング機能実装のため最初の10件に絞り込み
    this.currentEmployeeList = this.$store.getters.getAllEmployees;
  }
  /**
   * 現在表示されている従業員一覧の数を返す.
   *
   * @returns 現在表示されている従業員一覧の数
   */
  get getEmployeeCount(): number {
    return this.currentEmployeeList.length;
  } //従業員名
  /**
   * 従業員曖昧検索
   */

  private name = "";
  private searchList = [];
  private searchErrorMessage = "";
  //Vuexストア内、gettersの絞り込まれたemployeeオブジェクトを取得し返す
  searchName(): Array<Employee> {
    // return this.$store.getters.getSearchEmployeeByName(this.name);
    return (this.currentEmployeeList = this.$store.getters.getSearchEmployeeByName(
      this.name
    ));
  }
  //  if(getSearchEmployeeByName.indexOf(name) != -1){
  //     this.searchList.push(this.name)
  //   }else{
  //   this.searchErrorMessage ="1件もありませんでしたので全件表示します"
  //   }
}
</script>

<style scoped>
.searchForm {
  margin-bottom: 20px;
  width: 450px;
  margin: 0 auto;
}

.searchBtn {
  display: block;
  width: 150px;
  margin: 0 auto;
}
.serch {
  text-align: center;
}
</style>
