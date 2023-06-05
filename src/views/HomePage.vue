<template>
  <main>
    <SearchBar @search="searchVideos" />
    <section class="video-grid">
      <div v-for="video in filteredVideos" :key="video.id">
        <VideoItem
          v-if="video.title && video.bestThumbnail && video.views"
          :video="video"
        />
      </div>
    </section>
    <div class="no-videos" v-if="filteredVideos.length === 0">
      <p>No videos found.<br />Please try again!</p>
    </div>
  </main>
</template>

<script>
import axios from "axios";
import VideoItem from "../components/VideoItem.vue";
import SearchBar from "../components/SearchBar.vue";

export default {
  components: { VideoItem, SearchBar },
  name: "HomePage",
  data() {
    return {
      videos: [],
      searchQuery: "Traversy Media",
    };
  },
  mounted() {
    this.fetchVideos();
  },
  methods: {
    searchVideos(query) {
      this.searchQuery = query;
      this.fetchVideos();
    },
    fetchVideos() {
      const options = {
        method: "GET",
        url: 'https://youtube-search-results.p.rapidapi.com/youtube-search/',
        params: { q: this.searchQuery },
        headers: {
          'X-RapidAPI-Key': '9b8ff8bafamshdf787d6c9ab18e3p152ed8jsn77e66d5da94c',
          'X-RapidAPI-Host': 'youtube-search-results.p.rapidapi.com'
        },
      };
      axios
        .request(options)
        .then((response) => {
          console.log(response.data.items, "Hola");
          this.videos = response.data.items;
        })
        .catch((error) => {
          console.error(error);
        });
    },
  },
  computed: {
    filteredVideos() {
      if (!this.searchQuery) {
        return this.videos;
      }

      return this.videos.filter(
        (video) => video.title && video.bestThumbnail && video.views
      );
    },
  },
};
</script>

<style scoped>
.video-grid {
  display: grid;
  grid-gap: 20px;
}
.no-videos {
  display: flex;
  justify-content: center;
  align-content: center;
  margin-top: 3rem;
}
.no-videos p {
  font-size: 1rem;
  font-weight: bold;
}
@media (min-width: 600px) {
  .video-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}
@media (min-width: 900px) {
  .video-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}
</style>
