<template>
  <main class="container">
    <article class="vital_show row justify-content-center">
      <section class="mt-4 col-md-12">
        <h2 class="border-bottom border-secondary pb-2 mb-3">
          バイタル一覧(月別{{ vitalsLength }}件)
        </h2>
        <div
          v-if="!Object.values(weatherData).length"
          class="alert alert-danger"
          role="alert"
        >
          本日の<router-link to="/"><u>天候情報</u></router-link
          >を登録しないとバイタル新規登録はできません
        </div>

        <ul class="row list-unstyled">
          <li class="col-md-3 mb-2">
            <button
              type="button"
              class="btn btn-primary fs18 w-100 h-100"
              :disabled="!Object.values(weatherData).length"
              @click="openModel('modal-vital-post')"
            >
              バイタル登録
            </button>
          </li>
          <li class="col-md-4">
            <InputForm
              :selectlist="userNameList"
              name="user_id"
              :value="tableProps.user_id"
              @inputForm="inputForm"
              >利用者</InputForm
            >
          </li>
          <li class="col-md-3">
            <InputForm
              type="month"
              name="year_month"
              :value="tableProps.year_month"
              @inputForm="inputForm"
              >年月</InputForm
            >
          </li>
        </ul>
        <VitalTable
          v-if="vitalsList"
          :vitals-list="vitalsList"
          :table-props="tableProps"
        ></VitalTable>
      </section>
    </article>
    <!-- バイタル新規登録ボタンを押下したときに展開するバイタル新規登録モーダル -->
    <VitalModal id="modal-vital-post"
      >バイタル登録
      <template #input>
        <VitalInput
          input-type="vitalRegist"
          model-id="modal-vital-post"
          @scrollTop="scrollTop"
          ><template #button>登録</template></VitalInput
        ></template
      ></VitalModal
    >
    <!-- バイタル新規登録ボタンを押下したときに展開するバイタル更新モーダル -->
    <VitalModal id="modal-vital-put"
      >バイタル更新
      <template #input>
        <VitalInput
          input-type="vitalEdit"
          model-id="modal-vital-put"
          @scrollTop="scrollTop"
          ><template #button>更新</template></VitalInput
        ></template
      ></VitalModal
    >
  </main>
</template>
<script>
import VitalTable from '@/components/Vital/VitalTable'
import InputForm from '@/components/Common/InputForm.vue'
import VitalModal from '@/components/Vital/VitalModal.vue'
import VitalInput from '@/components/Vital/VitalInput.vue'
import moment from 'moment'
moment.locale('ja')

export default {
  name: 'Vital',
  components: {
    VitalTable,
    InputForm,
    VitalModal,
    VitalInput,
  },
  data() {
    return {
      today: moment(),
    }
  },
  computed: {
    tableProps() {
      return this.$store.getters['vital/tableProps']
    },
    vitalsList() {
      return this.$store.getters['vital/vitalsList']
    },
    vitalsLength() {
      return this.vitalsList.length
    },
    usersList() {
      return this.$store.getters['user/usersList']
    },
    userNameList() {
      return this.usersList.map((data) => ({
        value: data.id,
        name: data.id + '：' + data.name,
      }))
    },
    weatherData() {
      return this.$store.getters['weather/weatherData']
    },
  },
  // userとdayの入力が変わる度にDBからデータを取得する
  watch: {
    tableProps: {
      handler: function (tableProps) {
        this.$store.dispatch('vital/vitalsListSet', tableProps)
      },
      deep: true,
    },
  },
  created() {
    var day = this.today.format('YYYY-MM-DD')
    this.$store.dispatch('weather/createWeather', day)
    // バイタル入力画面で選択する利用者一覧をあらかじめ読み込んでおく
    this.$store.dispatch('user/usersListSet')
  },
  // ルート変更時にバイタル一覧をリセットする
  destroyed() {
    this.$store.dispatch('vital/resetList')
  },
  methods: {
    inputForm(e) {
      this.$store.dispatch('vital/stateInput', (state) => {
        state.tableProps[e.name] = e.value
      })
    },
    openModel(modelid) {
      this.$bvModal.show(modelid)
    },
    // 進行ボタンが押される度にモーダルスクロールを上部に移動
    scrollTop(e) {
      document.getElementById(e).scrollTo({
        top: 0,
        behavior: 'instant',
      })
    },
  },
}
</script>
