<template>
  <div class="bg-[#121212] min-h-screen text-white">
    <!-- Navigation Bar -->
    <div class="bg-[#121212] py-4 px-8 sticky top-0 z-10">
      <div class="flex items-center justify-between">
        <div class="flex items-center space-x-4">
          <!-- <button class="bg-black rounded-full p-2">←</button>
          <button class="bg-black rounded-full p-2">→</button> -->
        </div>
        <div class="flex items-center space-x-4">
          <button class="bg-white text-black px-8 py-2 rounded-full font-medium hover:scale-105">Sign up</button>
          <button class="bg-black px-8 py-2 rounded-full font-medium hover:bg-gray-900">Log in</button>
        </div>
      </div>
    </div>

    <div class="p-8">
      <h1 class="text-2xl font-bold mb-6">Popular Artists</h1>
      
      <!-- Artists Grid -->
      <div v-if="!selectedArtist" class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-5 gap-6">
        <div 
          v-for="artist in artists" 
          :key="artist.id"
          @click="selectArtist(artist)"
          class="cursor-pointer group bg-[#181818] rounded-lg p-4 hover:bg-[#282828] transition-all duration-300"
        >
          <div class="relative mb-4">
            <img 
              :src="artist.profile_picture" 
              :alt="artist.name"
              class="w-full aspect-square object-cover rounded-full shadow-lg"
            />
            <button 
              class="absolute bottom-2 right-2 opacity-0 group-hover:opacity-100 transition-all duration-300 bg-green-500 rounded-full p-3 hover:scale-105 shadow-lg translate-y-2 group-hover:translate-y-0"
            >
              <div class="w-6 h-6 text-black">▶</div>
            </button>
          </div>
          <h3 class="text-base font-bold truncate">{{ artist.name }}</h3>
          <p class="text-sm text-gray-400 mt-1">Artist</p>
        </div>
      </div>

      <!-- Artist Detail View -->
      <div v-else>
        <button 
          @click="selectedArtist = null"
          class="text-gray-400 hover:text-white mb-4 flex items-center space-x-2"
        >
          <span>←</span>
          <span>Back to Artists</span>
        </button>

        <!-- Artist Header -->
        <div class="flex items-end space-x-6 bg-gradient-to-b from-gray-600 to-[#121212] p-8 rounded-lg">
          <img 
            :src="selectedArtist.profile_picture" 
            :alt="selectedArtist.name"
            class="w-56 h-56 rounded-full shadow-2xl object-cover"
          />
          <div class="mb-4">
            <p class="text-sm font-medium mb-2">Artist</p>
            <h2 class="text-6xl font-bold mb-6">{{ selectedArtist.name }}</h2>
            <p class="text-gray-400 text-sm">Popular Tracks</p>
          </div>
        </div>

        <!-- Play Button Bar -->
        <div class="flex items-center space-x-4 py-4">
          <button class="bg-green-500 rounded-full p-4 hover:scale-105 transition-transform">
            <div class="w-6 h-6 text-black">▶</div>
          </button>
          <button class="text-gray-400 hover:text-white">Follow</button>
          <button class="text-gray-400 hover:text-white">•••</button>
        </div>

        <!-- Songs List -->
        <div class="mt-8">
          <h3 class="text-2xl font-bold mb-4">Popular</h3>
          <div class="space-y-2">
            <div 
              v-for="(song, index) in artistSongs" 
              :key="song.id"
              class="flex items-center p-3 hover:bg-[#282828] rounded-md group cursor-pointer"
            >
              <div class="w-8 text-gray-400 text-right mr-4">{{ index + 1 }}</div>
              <div class="flex-1">
                <div class="flex items-center space-x-4">
                  <img 
                    :src="song.album.cover_image" 
                    :alt="song.album.name"
                    class="w-12 h-12 object-cover"
                  />
                  <div>
                    <p class="font-medium">{{ song.name }}</p>
                    <p class="text-sm text-gray-400">{{ song.album.name }}</p>
                  </div>
                </div>
              </div>
              <div class="flex items-center space-x-4 opacity-0 group-hover:opacity-100 transition-opacity">
                <button class="text-gray-400 hover:text-white">♡</button>
                <audio :src="song.file_url" controls class="h-8"></audio>
                <button class="text-gray-400 hover:text-white">•••</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      artists: [],
      selectedArtist: null,
      artistSongs: []
    }
  },
  async created() {
    try {
      const response = await axios.get('http://127.0.0.1:8000/api/artists');
      this.artists = response.data;
    } catch (error) {
      console.error('Error fetching artists:', error);
    }
  },
  methods: {
    async selectArtist(artist) {
      this.selectedArtist = artist;
      try {
        const response = await axios.get('http://127.0.0.1:8000/api/songs');
        this.artistSongs = response.data.filter(song => song.artist_id === artist.id);
      } catch (error) {
        console.error('Error fetching songs:', error);
      }
    }
  }
}
</script>

<style>
audio {
  filter: invert(1);
  height: 32px;
  opacity: 0.8;
}

audio::-webkit-media-controls-enclosure {
  border-radius: 4px;
  background-color: transparent;
}
</style>