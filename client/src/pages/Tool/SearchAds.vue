<template>
  <div class="p-5">
    <div class="container">
      <h2>Search ADS PAGE ID</h2>
      <form>
        <div class="form-group">
          <label for="usr">Từ khóa tìm kiếm:</label>
          <textarea class="form-control" rows="5" id="list_keyword" :disabled="isDisabledScan" v-model="form.list_keyword"></textarea>
          <!-- <input
            type="text"
            class="form-control"
            id="q"
            :disabled="isDisabledScan"
            v-model="form.q"
          /> -->
        </div>
        <div class="form-group">
          <label for="sel1">Chọn quốc gia:</label>
          <select
            class="form-control"
            id="sel1"
            :disabled="isDisabledScan"
            v-model="form.country"
          >
            <option
              v-for="(country, index) in array_country"
              :key="index"
              :value="country"
            >
              {{ country }}
            </option>
          </select>
        </div>
        <div class="text-right m-3">
          <button
            type="button"
            class="btn btn-primary"
            :disabled="isDisabledScan"
            @click="submitScanPage()"
          >
            Scan Page
          </button>
          <button
            v-if="isDisabledScan"
            type="button"
            class="btn btn-primary"
            @click="ResumeScan()"
          >
            Tạm dừng
          </button>
          <button
            type="button"
            class="btn btn-warning"
            :disabled="isDisabledOpenPage"
            @click="openPage()"
          >
            Open Tab
          </button>
        </div>
      </form>
    </div>
    <div class="container">
      <div>
        <span class="badge bg-primary"> Tổng Page: {{ ArrayPage.length }}</span>
        <span class="badge bg-success">
          Tổng Page Đã Lọc Trùng: {{ filteredArray.length }}</span
        >
      </div>
      <div class="form-group p-3">
        <label for="page_data">Page Data:</label>
        <button type="button" class="btn btn-sm btn-warning m-2" @click="copyToClipboard(page_data)">Copy</button>
        <textarea class="form-control" rows="10" id="page_data" :disabled="true" v-model="page_data"></textarea>
      </div>
      <div class="form-group p-3">
        <label for="page_data_filtered">Page Data Lọc Trùng:</label>
        <button type="button" class="btn btn-sm btn-warning m-2" @click="copyToClipboard(page_data_filtered)">Copy</button>
        <textarea class="form-control" rows="10" id="page_data_filtered" :disabled="true" v-model="page_data_filtered"></textarea>
      </div>
    </div>
    <div class="container overflow-auto">
      <b-table
        v-if="filteredArray.length > 0"
        id="my-table"
        :items="filteredArray"
        :fields="fields"
        :per-page="perPage"
        :current-page="currentPage"
        small
      >
        <template #cell(index)="data">
          {{ filteredArray.indexOf(data.item) + 1 }}
        </template>
        <template #cell(status)="data">
          <b-badge variant="success" class="active-row" v-if="data.item.isActive"
            >Hoạt động</b-badge
          >
          <b-badge variant="danger" class="inactive-row" v-else>Không hoạt động</b-badge>
        </template>
        <template #cell(action)="data">
          <span
            ><a
              target="_blank"
              rel="noopener noreferrer"
              :href="`${data.item.page_profile_uri}`"
              >Open Page</a
            ></span
          >
        </template>
      </b-table>
      <b-pagination
        v-if="filteredArray.length > perPage"
        v-model="currentPage"
        :total-rows="filteredArray.length"
        :per-page="perPage"
        aria-controls="my-table"
      ></b-pagination>
    </div>
  </div>
</template>
<script>
import { API } from "@/helpers/api";

export default {
  data() {
    return {
      array_country: [
        "ALL",
        "BR",
        "IN",
        "GB",
        "US",
        "CA",
        " AR",
        " AU",
        " AT",
        " BE",
        " CL",
        " CN",
        " CO",
        " HR",
        " DK",
        " DO",
        " EG",
        " FI",
        " FR",
        " DE",
        " GR",
        " HK",
        " ID",
        " IE",
        " IL",
        " IT",
        " JP",
        " JO",
        " KW",
        " LB",
        " MY",
        " MX",
        " NL",
        " NZ",
        " NG",
        " NO",
        " PK",
        " PA",
        " PE",
        " PH",
        " PL",
        " RU",
        " SA",
        " RS",
        " SG",
        " ZA",
        " KR",
        " ES",
        " SE",
        " CH",
        " TW",
        " TH",
        " TR",
        " AE",
        " VE",
        " PT",
        " LU",
        " BG",
        " CZ",
        " SI",
        " IS",
        " SK",
        " LT",
        " TT",
        " BD",
        " LK",
        " KE",
        " HU",
        " MA",
        " CY",
        " JM",
        " EC",
        " RO",
        " BO",
        " GT",
        " CR",
        " QA",
        " SV",
        " HN",
        " NI",
        " PY",
        " UY",
        " PR",
        " BA",
        " PS",
        " TN",
        " BH",
        " VN",
        " GH",
        " MU",
        " UA",
        " MT",
        " BS",
        " MV",
        " OM",
        " MK",
        " LV",
        " EE",
        " IQ",
        " DZ",
        " AL",
        " NP",
        " MO",
        " ME",
        " SN",
        " GE",
        " BN",
        " UG",
        " GP",
        " BB",
        " AZ",
        " TZ",
        " LY",
        " MQ",
        " CM",
        " BW",
        " ET",
        " KZ",
        " NA",
        " MG",
        " NC",
        " MD",
        " FJ",
        " BY",
        " JE",
        " GU",
        " YE",
        " ZM",
        " IM",
        " HT",
        " KH",
        " AW",
        " PF",
        " AF",
        " BM",
        " GY",
        " AM",
        " MW",
        " AG",
        " RW",
        " GG",
        " GM",
        " FO",
        " LC",
        " KY",
        " BJ",
        " AD",
        " GD",
        " VI",
        " BZ",
        " VC",
        " MN",
        " MZ",
        " ML",
        " AO",
        " GF",
        " UZ",
        " DJ",
        " BF",
        " MC",
        " TG",
        " GL",
        " GA",
        " GI",
        " CD",
        " KG",
        " PG",
        " BT",
        " KN",
        " SZ",
        " LS",
        " LA",
        " LI",
        " MP",
        " SR",
        " SC",
        " VG",
        " TC",
        " DM",
        " MR",
        " AX",
        " SM",
        " SL",
        " NE",
        " CG",
        " AI",
        " YT",
        " CV",
        " GN",
        " TM",
        " BI",
        " TJ",
        " VU",
        " SB",
        " ER",
        " WS",
        " AS",
        " FK",
        " GQ",
        " TO",
        " KM",
        " PW",
        " FM",
        " CF",
        " SO",
        " MH",
        " VA",
        " TD",
        " KI",
        " ST",
        " TV",
        " NR",
        " RE",
        " LR",
        " ZW",
        " CI",
        " MM",
        " AN",
        " AQ",
        " BQ",
        " BV",
        " IO",
        " CX",
        " CC",
        " CK",
        " CW",
        " TF",
        " GW",
        " HM",
        " XK",
        " MS",
        " NU",
        " NF",
        " PN",
        " BL",
        " SH",
        " MF",
        " PM",
        " SX",
        " GS",
        " SS",
        " SJ",
        " TL",
        " TK",
        " UM",
        " WF",
        " EH",
      ],
      form: {
        list_keyword: '',
        q: "",
        country: "US",
        count: 30,
        dataHtml: null,
        inputforwardCursor: "",
        inputcollationToken: "",
      },
      loading: false,
      isDisabledScan: false,
      isResumeScan: false,
      isDisabledOpenPage: false,
      ArrayPage: [],
      filteredArray: [],
      page_data: "",
      page_data_filtered: "",
      perPage: 30,
      currentPage: 1,
      fields: [
        { key: "index", label: "Index" },
        { key: "pageID", label: "Page ID" },
        { key: "pageName", label: "Page Name" },
        { key: "status", label: "Trạng thái" },
        { key: "action", label: "Action" },
      ],
    };
  },
  computed: {
    currentItems() {
      // Tính toán các mục trên trang hiện tại dựa trên currentPage và perPage
      const startIndex = (this.currentPage - 1) * this.perPage;
      const endIndex = startIndex + this.perPage;
      return this.filteredArray.slice(startIndex, endIndex);
    },
  },
  watch: {
    ArrayPage: async function (value) {
      this.page_data = (
        await Promise.all(
          value.map((item) => `${item.pageID}`)
        )
      ).join("\r\n");
      this.filteredArray = await Promise.all(
        value.reduce((uniqueArray, currentItem) => {
          // Kiểm tra xem pageID đã tồn tại trong uniqueArray chưa
          const isDuplicate = uniqueArray.some(
            (item) => item.pageID === currentItem.pageID
          );

          // Nếu chưa tồn tại, thêm đối tượng vào uniqueArray
          if (!isDuplicate) {
            uniqueArray.push({
              pageID: currentItem.pageID,
              pageName: currentItem.pageName,
              isActive: currentItem.isActive,
              page_profile_uri: currentItem.page_profile_uri
            });
          }

          return uniqueArray;
        }, [])
      );
    },
    filteredArray: async function (value) {
      this.page_data_filtered = (
        await Promise.all(
          value.map((item) => `${item.pageID}`)
        )
      ).join("\r\n");
    },
  },
  methods: {
    copyToClipboard(text) {
      const textarea = document.createElement("textarea");
      textarea.value = text;
      document.body.appendChild(textarea);
      textarea.select();
      document.execCommand("copy");
      document.body.removeChild(textarea);
    },
    openPage: function () {
      this.loading = true;
      this.isDisabledOpenPage = true;
      try {
        // this.currentItems.forEach((item, index) => {
        //   setTimeout(() => {
        //     this.openNewTab(`${item.page_profile_uri}`);
        //   }, index * 2000); // Mỗi lần mở tab chờ 2 giây (2000 mili giây)
        // });
        this.currentItems.forEach((item, index) => {
          this.openNewTab(`${item.page_profile_uri}`);
        });
      } catch (error) {
        console.log(error);
      } finally {
        this.loading = false;
        this.isDisabledOpenPage = false;
      }
    },
    submitScanPage: async function () {
      this.loading = true;
      this.isDisabledScan = true;
      this.isResumeScan = false;
      try {
        const promises = this.form.list_keyword.split('\n').filter(item => item && item.trim() !== '').map(item => this.scanPage(item.trim()));
        await Promise.all(promises);
      } catch (error) {
        console.log(error);
      } finally {
        this.loading = false;
        this.isDisabledScan = false;
        this.isResumeScan = true;
      }
    },
    scanPage: async function(keyword){
      try {
        let formPost = {
          q: keyword,
          country: this.form.country,
          count: this.form.count,
          dataHtml: null,
          inputforwardCursor: "",
          inputcollationToken: "",
        }
        let dem_page = 0;
        const getSearchAds = await API().post("/facebook/get_search_ads", formPost);
        if (getSearchAds.data.success) {
          if (dem_page == 0) {
            formPost.dataHtml = getSearchAds.data.data;
          }
          if (!formPost.dataHtml) {
            return alert("Không thể get dữ liệu");
          }
          await this.delay(500);
          while (true) {
            const searchAds = await API().post("/facebook/search_ads", formPost);
            if (searchAds.data.success) {
              this.ArrayPage = this.ArrayPage.concat([...searchAds.data.data]);
              formPost.inputforwardCursor = searchAds.data.forwardCursor;
              formPost.inputcollationToken = searchAds.data.collationToken;
              if (
                !searchAds.data.forwardCursor ||
                searchAds.data.forwardCursor == "" ||
                !searchAds.data.collationToken ||
                searchAds.data.collationToken == ""
              ) {
                break;
              }
              if (this.isResumeScan) {
                break;
              }
              dem_page++;
              await this.delay(5000);
            } else {
              break;
            }
          }
          return;
        }
        return;
      } catch (error) {
        console.log(error)
        return;
      }
    },
    ResumeScan: function () {
      this.isDisabledScan = false;
      this.isResumeScan = true;
    },
    delay: (delayInms) => {
      return new Promise((resolve) => setTimeout(resolve, delayInms));
    },
    openNewTab(url) {
      window.open(url, "_blank");
    },
  },
};
</script>
<style lang="css" scoped>
.active-row {
  background-color: #28a745; /* Màu xanh cho hàng hoạt động */
}

.inactive-row {
  background-color: #dc3545; /* Màu đỏ cho hàng không hoạt động */
}
</style>
