<template>
  <div>
    <div
      class="header pb-8 pt-5 pt-lg-8 d-flex align-items-center profile-header"
    >
      <b-container v-if="!isResult" fluid class="mt--6">
        <b-row>
          <b-col xl="12" class="mb-5">
            <UploadImages style="max-width: 100%" @changed="handleImages" />
            <vue-element-loading
              :active="isloading"
              is-full-screen
              spinner="bar-fade-scale"
            />
          </b-col>
          <!-- <b-col xl="8" >
          </b-col> -->
        </b-row>
        <b-row>
          <b-col xl="12" class="mb-5">
            <b-button v-on:click="submitFile" variant="success" block size="lg"
              >Submit images</b-button
            >
          </b-col>
        </b-row>
      </b-container>

      <b-container v-if="isResult" fluid class="mt--6">
        <b-row>
          <b-col xl="12" class="mb-5">
            <b-table-simple hover small caption-top stacked>
              <caption>
                Please see the results below:
              </caption>
              <colgroup>
                <col />
                <col />
              </colgroup>
              <colgroup>
                <col />
                <col />
                <col />
              </colgroup>
              <colgroup>
                <col />
                <col />
              </colgroup>
              <b-tbody>
                <b-tr v-for="(serverResult, index) in resultFromServer" :key="serverResult.text">
                  <b-td variant="info" stacked-heading="Index">#{{index + 1}}</b-td>
                  <b-td><b-img :src="getImageUrl(serverResult.image)" fluid alt="Request image"></b-img></b-td>
                  <b-td variant="success"><b>Text:</b></b-td>
                  <b-td>{{serverResult.text}}</b-td>
                  <b-td v-for="tag in serverResult.tag" :key="tag" stacked-heading="Tag" variant="success"><b-button style="margin-right: 20px;">Confidence: {{numberFormatter(tag.confidence)}}</b-button><b-button variant="success">{{tag.word}}</b-button></b-td>
                </b-tr>
              </b-tbody>
            </b-table-simple>
          </b-col>
          <!-- <b-col xl="8" >
          </b-col> -->
        </b-row>
        <b-row>
          <b-col xl="12" class="mb-5">
            <b-button v-on:click="goback" variant="success" block size="lg"
              >Go back!</b-button
            >
          </b-col>
        </b-row>
      </b-container>
    </div>
  </div>
</template>

<script>
const axios = require("axios");
import UploadImages from "vue-upload-drop-images";
import VueElementLoading from "vue-element-loading";

export default {
  /*
      Defines the data used by the component
    */
  components: {
    UploadImages,
    VueElementLoading,
  },
  data() {
    return {
      files: [],
      isloading: false,
      isResult: false,
      after_result_images: [],
      resultFromServer: [],
      items: [
        { age: 40, first_name: "Dickerson", last_name: "Macdonald" },
        { age: 21, first_name: "Larsen", last_name: "Shaw" },
        { age: 89, first_name: "Geneva", last_name: "Wilson" },
        { age: 38, first_name: "Jami", last_name: "Carney" },
      ],
    };
  },

  methods: {
    /*
        Submits the file to the server
      */
    numberFormatter(num) {
      return num.toFixed(2);
    },
    getImageUrl(image_file){
      // debugger; // eslint-disable-line no-debugger
      console.log(image_file)
      const file = image_file
      let url = URL.createObjectURL(file);
      return url 

    },
    goback() {
      this.isResult = false;
      this.resultFromServer = []
      this.after_result_images = []
      console.log(this.resultFromServer)
    },
    submitFile() {
      this.isloading = true;
      let that = this;
      /*
                Initialize the form data
            */
      let formData = new FormData();

      /*
                Add the form data we need to submit
            */
      this.files.forEach((f) => {
        formData.append("imagefile", f);
      });

      /*
          Make the request to the POST /single-file URL
        */
      axios
        .post("http://18.142.23.26:8000/api/upload", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then((response) => {
          console.log("SUCCESS!!");
          console.log(response.data);
          that.isloading = false;
          that.after_result_images = that.files 
          that.files = []
          this.isResult = true;
          // this.resultFromServers
          response.data.result.forEach(function(element, i) {
            console.log(element)
            let current_result = {}
            current_result["image"] = that.after_result_images[i]
            current_result["text"] = element.text
            current_result["tag"] = element.ner
            that.resultFromServer.push(current_result)
          });
          
        })
        .catch((response) => {
          console.log("FAILURE!!");
          console.log(response);
          that.isloading = false;
        });
    },

    /*
        Handles a change on the file upload
      */
    handleImages(files) {
      // this.show = true;
      // var that = this;
      // setTimeout(function () {
      //   that.show = false;
      // }, 3000);
      console.log(files);
      this.files = files;
      /*
                  [
                    {
                        "name": "Screenshot from 2021-02-23 12-36-33.png",
                        "size": 319775,
                        "type": "image/png",
                        "lastModified": 1614080193596
                        ...
                    },
                    ...
                    ]
                 */
    },
  },
};
</script>