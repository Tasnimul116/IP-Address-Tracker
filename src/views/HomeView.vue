<template>
  <div class="flex flex-col h-screen max-h-screen">
    <!--Search-->

    <div class="z-20 flex justify-center relative bg-hero-pattern bg-cover  px-4 py-8 pb-32">

      <!--search input-->

      <div class="w-full max-w-screen-sm"> 
      <h1 class="text-white text-center text-3xl pb-4">IP Address Tracker</h1>
      <div class="flex">

        <input class="flex-1 py-3 px-2 rounded-tl-md rounded-bl-md focus:outline-none" type="text"
        placeholder="Input your IP "
        v-model="queryIp"
        />
        <i @click="getIpInfo" class="fas fa-chevron-right cursor-pointer text-white bg-black px-4 rounded-tr-md rounded-br-md flex items-center"></i>
      </div>
      </div>

     <!--IP Info-->

      <IPinfo v-if="IpInfo" v-bind:IpInfo="IpInfo"/>

    </div>

    <!--Map-->

    <div id="mapid" class="h-full z-10 "></div>
  </div>
</template>
<script>
import IPinfo from '@/components/IPinfo.vue';
import leaflet from 'leaflet';
import { onMounted ,ref} from 'vue';
import axios from 'axios'

export default {
  name: 'HomeView',
  components: {
    IPinfo
  },
  setup() {
    let mymap;
    const queryIp = ref();
    const IpInfo = ref(null)
    onMounted(() => 
    {
      mymap = leaflet.map('mapid').setView([42.5145, -83.0147], 9);
      leaflet.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', 
      {
         maxZoom: 19,
         attribution: `&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>`
       }).addTo(mymap);


    });

    const getIpInfo = async() => {
  try {
    const data = await axios.get(`https://geo.ipify.org/api/v2/country?apiKey=at_mRXc8CyySHz15FDIsxTWTTJMRCDdO&ipAddress=${queryIp.value}`);
    const result = data.data;
    IpInfo.value = {
      address: result.ip,
      state: result.location.region,
      timezone: result.location.timezone,
      isp: result.isp,
      lat: result.location.lat,
      lng: result.location.lng,
    };
    if (IpInfo.value.lat && IpInfo.value.lng) {
     
      leaflet.marker([IpInfo.value.lat, IpInfo.value.lng]).addTo(mymap);
      mymap.setView([IpInfo.value.lat, IpInfo.value.lng], 13);
    } else {
      console.error('Latitude or longitude is undefined or null.');
    }
  } catch(err) {
    alert(err.message);
  }
}






    return{queryIp,IpInfo,getIpInfo}
  }
};
</script>