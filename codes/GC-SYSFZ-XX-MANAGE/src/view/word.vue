<template>
  <div class="pdf">
    <div ref="file"></div>
  </div>
  　
</template>

<script>
import Axios from "axios";

// import { renderAsync } from "docx-preview";
// let docx = require("docx-preview");
export default {
  created() {
    const params = this.$route.query;
    console.log(
      process.env.BASE_API + "upload/" + params.url,
      'process.env.BASE_API + "upload/" + params.url'
    );
    // this.pdfUrl = process.env.BASE_API + "download/" + params.url;
    Axios({
      method: "get",
      responseType: "blob",
      url: "http://192.168.2.150:8068/upload/" + params.url,
    }).then(({ data }) => {
      // docx.renderAsync(data, this.$refs.file);
    });
  },
  data() {
    return {};
  },
};
</script>
