<template>
  <div>
    <div
      class="smokeBg"
      :style="{
        backgroundImage: `url(https://image.tmdb.org/t/p/original${this.bgList})`,
      }"
    >
      <div id="searchFrm">
        <form @submit.prevent="onSearch" class="search-box">
          <input
            type="text"
            id="keyword"
            name="keyword"
            v-model="keyword"
            placeholder="영화를 검색해주세요!"
            class="input-text"
            title="검색어를 입력해 주세요"
          />
        </form>
        <button
          type="button"
          class="btn-search"
          title="검색하기"
          @click="onSearch"
        >
          검색
        </button>
      </div>
    </div>
    <MovieList :movieList="movieList" :defaultMessage="defaultMessage" />
  </div>
</template>

<script>
import { search } from "@/api/index.js";
import MovieList from "./MovieList";
export default {
  data() {
    return {
      keyword: "",
      movieList: "",
      defaultMessage: "검색된영화가 존재하지않습니다.",
      bgList: {},
    };
  },
  components: {
    MovieList,
  },
  methods: {
    async onSearch() {
      if (!this.keyword) {
        alert("영화 제목을 입력하세요!");
        this.keyword = "";
        return;
      }
      await search(this.keyword)
        .then(({ data }) => {
          this.defaultMessage = `검색하신 ' ${this.keyword} '의 검색으로 총 ' ${data.results.length} ' 개의 결과를 찾았습니다.`;
          this.movieList = data.results;
          this.keyword = "";
        })
        .catch(err => {
          console.log(err);
        });
    },
  },
  computed: {
    cp() {
      return this.$store.state.movie.movieId;
    },
  },
  watch: {
    cp(a) {
      const arr = a.map(a => a.backdrop_path);
      const chosen = arr.splice(Math.floor(Math.random() * arr.length), 1)[0];
      //랜덤숫자 하나 뽑고
      if (!chosen == "") {
        this.bgList = chosen;
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.smokeBg {
  min-height: 250px;
  overflow: hidden;
  background-size: cover;
  background-position: bottom;
  position: relative;
  //background-image: url("~@/assets/images/imgbg.jpg");
  background-attachment: fixed;
  &::before {
    content: "";
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    bottom: 0;
    background: linear-gradient(to top, #00000078, rgb(0 0 0 / 43%));
    display: block;
    -webkit-backdrop-filter: blur(20px);
    backdrop-filter: blur(20px);
    background-color: rgb(0 0 0 / 70%);
  }
  img {
    width: 100%;
  }
  #searchFrm {
    width: 515px;
    height: 100px;
    margin: auto;
    position: absolute;
    text-align: center;
    align-items: center;
    top: 0;
    right: 0;
    left: 0;
    bottom: 0;
    line-height: 100px;
    .search-box {
      display: inline-block;
      .selects {
        width: 100%;
        display: flex;
        position: relative;
        select {
          min-width: 100px;
          color: #fff;
          padding: 10px 15px;
          margin: 0 10px;
          background: #1b1b1b;
          border: 1px solid #454545;
          border-radius: 5px;
          font-size: 17px;
          option {
            background-color: #000;
            border: 1px solid #eee;
            font-size: 20px;
          }
        }
      }
      input {
        background: transparent;
        border: 1px solid transparent;
        border-bottom: 1px solid #454545;
        padding: 15px;
        color: #fff;
        font-size: 23px;
        min-width: 450px;
        display: inline-block;
        box-sizing: border-box;
        font-weight: 100;
        &:focus {
          border-top: 1px solid transparent;
          border-bottom: 1px solid #fff;
          outline: none;
        }
        &::placeholder {
          font-weight: 500;
          font-size: 23px;
          color: rgb(141, 141, 141);
        }
      }
    }
  }
  .btn-search {
    overflow: hidden;
    position: relative;
    right: 0;
    top: 0;
    width: 64px;
    height: 59px;
    display: inline-block;
    text-indent: -9999px;
    border: none;
    background: transparent url("~@/assets/images/btn-main-search.png")
      no-repeat center;
    &:hover {
      cursor: pointer;
    }
  }
}
</style>
