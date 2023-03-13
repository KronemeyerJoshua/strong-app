<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button color="primary"></ion-menu-button>
        </ion-buttons>
        <ion-title>{{ $route.params.id }}</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">{{ $route.params.id }}</ion-title>
        </ion-toolbar>
      </ion-header>

      <barbell-loader />
    </ion-content>
  </ion-page>
</template>

<script>
import { IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar } from '@ionic/vue';
import {defineComponent} from "vue";
import barbellLoader from "@/components/barloader/barbell-loader.vue";

export default defineComponent({
  Name: 'FolderPage',
  components: {
    IonButtons, IonContent, IonHeader, IonMenuButton, IonPage, IonTitle, IonToolbar, barbellLoader
  },
  data() {
    return {
      totalWeight: 45,
      plateNumber: 0,
    }
  },
  methods: {
    addWeight() {
      if (this.plateNumber > 7)
        return;

      this.plateNumber++;
      this.totalWeight += 90;
    },
    removeWeight() {
      if (this.plateNumber < 1)
        return;

      this.plateNumber--;
      this.totalWeight -= 90;
    }
  }
})
</script>

<style scoped>

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;
  color: #8c8c8c;
  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>
