<template>
 
    <div id="app" style="padding-right:30px;">
      <VirtualGrid
        :items="items"
        :getColumnCount="getColumnCount"
        ref="VirtualGridRef"
      />

      <div class="loading" v-if="loading">
        <span class="loading-text">Loading...</span>
      </div>
    </div>
 
</template>

<script>
import VirtualGrid from "vue-virtual-grid";
import Card from "./components/Card.vue";
export default {
  name: "App",
  components: {
    VirtualGrid,
  },
  data() {
    return {
      items: [],
      loading: false,
      current_page: 1,
      last_page: 1,
      columns: 4,
    };
  },
  methods: {
    loaderComponent() {
      return Load;
    },
    getColumnCount(elementWidth) {
      return this.columns; // Set the number of columns.
    },
    calculateColumnWidth() {
      const width = window.innerWidth;
      if (width < 600) {
        this.columns = 1; 
      } else if (width >= 600 && width < 960) {
        this.columns = 2; 
      } else if (width >= 960 && width < 1280) {
        this.columns = 3; 
      } else {
        this.columns = 4; 
      }
      this.items.forEach((item) => {
        const height  = this.calculateHeight(item.title);
        item.height = height;
        
      });
      
    },
    calculateHeight(text){
     
      const gap = 10; // gap between cards in pixels
      const width = window.innerWidth;
      const columnWidth = width / this.columns - gap;
      const extraHeight = 100; // Additional height if required

       //approche 1
      // const lineHeight = 40; // Adjust line height based on your text font and size

     

      // const textLength = text.length;
      // const lines = Math.ceil(textLength / (columnWidth / 16)); // Assuming 2rem font size

      // const approxHeight = lines * lineHeight;

      // return approxHeight + extraHeight;

      //approche 2
 
          //create dev
          const div = document.createElement("div");
          div.style.width = columnWidth + "px";
          div.style.position = "absolute";
          div.style.top = "0";
          div.style.left = "0";
          div.style.visibility = "hidden";
          div.style.fontSize = "2rem";
          div.style.boxSizing = "border-box";
          div.style.wordWrap = "break-word";
          div.style.whiteSpace = "normal";
          div.style.fontWeight = "400";
          div.style.fontStyle = "normal";
          div.style.padding = "10px";
          document.body.appendChild(div);
          div.innerHTML = text;
          const height = div.clientHeight;
          document.body.removeChild(div);
          
          
          return height + extraHeight;

      
    },
    getItems(item) {
      return {
        title: item.title,
        id: item.id,
        height: this.calculateHeight(item.title),
        columnSpan: 1,
        renderComponent: Card,
      }
    },
    getData() {
      this.loading = true;
      this.axios.get("https://boystack.com/api/getdata").then((res) => {
        this.loading = false;
        this.current_page = res.data.current_page;
        this.last_page = res.data.last_page;
        this.items = res.data.data.map((item) => {
          return this.getItems(item);
        });
      });
    },
    loadMore() {
      if (this.loading) return;
      if (this.current_page < this.last_page) {
        this.current_page++;
        this.loading = true;
        this.axios
          .get("https://boystack.com/api/getdata?page=" + this.current_page)
          .then((res) => {
            this.loading = false;
            this.current_page = res.data.current_page;
            this.last_page = res.data.last_page;
            this.items = this.items.concat(
              res.data.data.map((item) => {
                return this.getItems(item);
              })
            );
          });
      }
    },
  },
  mounted() {
    this.calculateColumnWidth();
    this.getData();
    //when scroll to bottom load more
    window.addEventListener("scroll", () => {
      if (
        window.innerHeight + window.scrollY >=
        document.body.offsetHeight - 100
      ) {
        this.loadMore();
      }
    });

    //when resize window calculate number of columns
    window.addEventListener("resize", () => {
      this.calculateColumnWidth();
    });
  },
};
</script>


<style scoped>
.loading {
  display: flex;
  justify-content: center;
  align-items: center;
  widows: 100%;
  padding-top: 50px;

}
.loading-text {
  margin: auto;
  text-align: center;
  font-size: 20px;
  font-weight: bold;
  color: #ccc;
  margin-top: 20px;
}
</style>