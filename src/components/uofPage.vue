<template>
  <div class="hello">
    <p>
      <label>Введите идентификатор: </label
      ><input v-model="name" placeholder="DS_DZHR1..." />
    </p>
    <p>
      <label>Введите 1 линию стрипа: </label
      ><input id="name" v-model="first_line" placeholder="1" />
    </p>
    <p>
      <label>Введите кол-во линий: </label
      ><input id="name"  v-model="number_of_lines" placeholder="624" />
    </p>
    <p>
      <label>Выберите уровень: </label>
      <select v-model="level">
        <option value="PL1A">L1</option>
        <option value="PL2A">L2</option>
        <option value="L2_RPC">L2RPC</option>
      </select>
      </p>
      <p>
      <label>Выберите расположение обработанного снимка: </label>
      <select v-model="destination">
        <option value="USR">cloud.gharysh.kz</option>
        <option value="PFPF">PFPF</option>
      </select>
    </p>
    <p>
      <label>ID: </label>
      <label>{{ ID_image }} </label>
    </p>
    <p>
    <button @click="post_dpc">Отправить на обработку</button>
    <button  id="btn1"  width="100" height="50" @click="getImage">Показать квиклук</button>
    <button  id="btn1"  width="100" height="50" @click="getLines">Получить линии</button>
    </p>
    <table >
      <td><img id="image_dpc" v-bind:src="image" width="100" alt="" ></td>
      <td ><Slider  id='slider_line'  v-model="value" :min = "1" :max="allLine" :style="{height: s_height + 'px'}"   orientation='vertical'   /></td>
    </table>
    
    
  </div>
</template>

<script>
import axios from "axios";
import Slider from '@vueform/slider'
export default {
 components: {
      Slider,
    },
  data() {
    return {
      value: [1, 625],
      name: "",
      level: 'PL1A',
      ID_image: "",
      image: null,
      destination: 'USR',
      allLine : 0,
      s_height: 0,
      count: 0
      
    };
  },
 
created() {
    let urlParams = new URLSearchParams(window.location.search);    
    console.log(urlParams.get('image_id')); 
    this.name = urlParams.get('image_id');
    
  },
  watch: {
    count(newCount){

      this.value[1] = newCount
    }
  },
  computed: {
    first_line: function () {
      return this.value[0]
    },
    number_of_lines: function() {
      return this.value[1] - this.value[0] + 1
    }, 
    values: function(){
      return [1, this.number_of_lines]
    }   
  },
  methods: {
    
    getImage() {       
        this.image = `https://eo.gharysh.kz/quicklooks/${this.name}_QL.jpeg`; 

    },

    getLines() {
      var img = document.getElementById("image_dpc");
      this.allLine = Math.round(img.naturalHeight / (img.naturalWidth / 125) * 5 - 5);
      this.s_height = img.height
    },

    async post_dpc() {
      console.log(this.number_of_lines)
      const order = `7|0|28|${BASE_URL}|22DE84F37F037DB3ECBD9FE761FA4944|net.eads.astrium.faceo.middleware.gwt.client.IProductRequestGWTService|submitProductRequest|net.eads.astrium.faceo.core.apis.productrequest.ProductRequestSubmitQuery/842192367|java.util.ArrayList/4159755760|net.eads.astrium.faceo.core.apis.productrequest.ProductRequestQueryValue/2319628165|HR_IMAGE_SERVICE_SEGMENT|${this.name}|HR_IMAGE_SERVICE_FIRST_EXTRACTED_LINE|${this.first_line}|HR_IMAGE_SERVICE_NUMBER_OF_EXTRACTED_LINE|${this.number_of_lines}|HR_PROCESSING_PARAMETERS_HR_PRIORITY|HR_PROCESSING_PARAMETERS_HR_PRIORITY_ROUTINE|HR_PROCESSING_PARAMETERS_HR_PRE_PROCESSING_LEVEL|HR_PROCESSING_PARAMETERS_HR_PRE_PROCESSING_LEVEL_${this.level}|HR_PROCESSING_PARAMETERS_HR_SPECTRAL_PROCESSING|HR_PROCESSING_PARAMETERS_HR_SPECTRAL_PROCESSING_MULTISPECTRAL|HR_OUTPUT_PRODUCT_CHARACTERISTICS_PRODUCT_DESTINATION|HR_OUTPUT_PRODUCT_CHARACTERISTICS_PRODUCT_DESTINATION_${this.destination}|HR_OUTPUT_PRODUCT_CHARACTERISTICS_PRODUCT_MEDIUM|HR_OUTPUT_PRODUCT_CHARACTERISTICS_PRODUCT_MEDIUM_NETWORK|HR_OUTPUT_PRODUCT_CHARACTERISTICS_PRODUCT_CODE|HR_OUTPUT_PRODUCT_CHARACTERISTICS_COMMERCIAL_REFERENCE||java.lang.Integer/3438268394|http://hruofs01:8080/|1|2|3|4|1|5|5|0|6|10|7|8|9|7|10|11|7|12|13|7|14|15|7|16|17|7|18|19|7|20|21|7|22|23|7|24|11|7|25|26|0|6|1|27|0|28|26|`;
      var BASE_URL =
        "http://hruofs01:8080/operator-office/net.eads.astrium.faceo.HomePage/";
      var uof_config = {
        baseURL: BASE_URL,
        headers: {
          "Accept-Language": "en-US,en;q=0.5",
          "Accept-Encoding": "gzip, deflate",
          "Content-Type": "text/x-gwt-rpc; charset=utf-8",
          "User-Agent":
            "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:102.0) Gecko/20100101 Firefox/102.0",
          "X-GWT-Permutation": "66578F84A8AD5079F3B2B687595D7FFB",
          "X-GWT-Module-Base": BASE_URL,
          "Access-Control-Allow-Origin": "*",
          Connection: "keep-alive",
        },
        withCredentials: true,
      };
      
      const connect = axios.create(uof_config);
      try {
        const resp = await connect.post("/productRequestService.rpc", order);

        if (resp.data.substring(0, 4) === "//OK") {
          const ind = resp.data.indexOf('HR_', 0);
          this.ID_image = resp.data.substring(ind, ind+16);
          console.log("ok");
        } else if (resp.data.substring(0, 4) === "//EX") {
          console.log("error1");
        } else {
          console.log("error2");
        }
      } catch (error) {
        return console.log("error 3");
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style src="@vueform/slider/themes/default.css"


></style>
