<template>
  <div class="container">
    <div class="screen">
      <p class="font-weight-bold title">Pantalla</p>
    </div>
    <div class="seates">
      <div class="row overflow-auto mt-4">
        <!-- Icons made by <a href="https://www.flaticon.com/authors/freepik" title="Freepik">Freepik</a> from <a href="https://www.flaticon.com/" title="Flaticon"> www.flaticon.com</a> -->

        <Seat v-for="(seat, index ) in seats" :key="index" :seat="seat" @selectSeat="selectSeat" />
      </div>
      <div class="buttons rounded">
        <header class="b-header">Options</header>
        <b-button variant="primary" class="rounded" @click="saveChanges">Save</b-button>
      </div>
    </div>
  </div>
</template>

<script>
import firebase from "firebase";
import Seat from "./Seat";

export default {
  components: {
    Seat
  },
  data() {
    return {
      seats: []
    };
  },
  created() {
    this.getSeats();
    //this.updateSeats ()
  },
  methods: {
    selectSeat(id) {
      let seat = this.seats.find(seat => seat.id === id);
      if (seat.adquirid ){
        console.log(`This seat has aquirided. Seat ${seat.row}${seat.number} is not avaliable`);
        return
      }
      seat.avaliable = !seat.avaliable;
    },
    getSeats() {
      firebase
        .database()
        .ref("rooms")
        .child("1")
        .once("value", snapshot => (this.seats = snapshot.val()));
    },
    updateSeats() {
      // let rows = ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J"];
      // for (const row of rows) {
      //   for (let index = 1; index < 15; index++) {
      //     let cod = `${index}`
      //     this.seats.push({
      //       id: `${row}`+ cod.padStart(4,0),
      //       row: `${row}`,
      //       number: `${index}`,
      //       avaliable: true,
      //       adquirid: false

      //     });
      //   }
      // }
      firebase
        .database()
        .ref("rooms")
        .child("1")
        .set(this.seats);
    },
    saveChanges() {
      this.updateSelectedSteats();
      this.updateSeats();
    },
    selectedSeats (){
      return this.seats.filter( seat=> !seat.avaliable && !seat.adquirid)
    },
    updateSelectedSteats () {
      this.selectedSeats().forEach( seat => seat.adquirid=true)
    }
  }
};
</script>

<style scoped>
.screen {
  background-color: rgba(28, 127, 185, 0.726);
}
.title {
  color: white;
  font-size: 18px;
}
.seats {
  margin: 20px auto;
}
.buttons {
  width: auto;
  position: fixed;

  bottom: 40%;
  right: 0;
  background-color: rgba(99, 162, 199, 0.356);
  padding: 10px;
}
.b-header {
  color: rgb(56, 52, 52);
  width: 100%;
  padding: 10px;
  border-radius: 10px;
  font-weight: bolder;
}
.rounded {
  border-radius: 50px !important;
}
</style>