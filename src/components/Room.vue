<template>
  <div class="container">
    <div class="screen">
      <p class="font-weight-bold title">Pantalla</p>
    </div>
    <div class="seates">
      <div class="row overflow-auto mt-4">
        <!-- Icons made by <a href="https://www.flaticon.com/authors/freepik" title="Freepik">Freepik</a> from <a href="https://www.flaticon.com/" title="Flaticon"> www.flaticon.com</a> -->

        <Seat
          v-for="(seat, index ) in seats"
          :key="index"
          :seat="seat"
          @selectSeat="selectSeat"
          :user="id"
        />
      </div>
      <div class="buttons rounded">
        <header class="b-header">Options</header>
        <div class="b-container bg-info text-white font-weight-bold p-1">
          <div>Selected seats :</div>
          <div>{{ counter }}</div>
        </div>
        <div class="b-container">
          <b-button
            variant="primary"
            :disabled="counter==0"
            class="rounded"
            @click="saveChanges"
          >Save</b-button>
        </div>
        <div class="b-container">
          <b-button
            variant="danger"
            :disabled="counter==0"
            class="rounded"
            @click="discardChanges"
          >Cancel</b-button>
        </div>
        <div class="b-container">
          <b-button class="rounded" @click="release">Relaese</b-button>
        </div>
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
      id: "",
      seats: [],
      counter: 0
    };
  },
  created() {
    this.id = firebase
      .database()
      .ref("/users")
      .push().key;
    this.getSeats();
    //this.updateSeats ()
  },
  methods: {
    selectSeat(id) {
      let seat = this.seats.find(seat => seat.id === id);
      if (seat.adquirid || (seat.user_id != null && seat.user_id != this.id)) {
        this.$notify({
          group: "foo",
          type: "error",
          title: "Error 😬",
          text: `This seat has been acquired. Seat ${seat.row}${seat.number} is not avaliable`
        });
        return;
      }
      seat.avaliable = !seat.avaliable;
      seat.user_id = this.id;
      this.updateSeats();
      this.counter = this.selectedSeats().length;
    },
    getSeats() {
      firebase
        .database()
        .ref("rooms")
        .child("1")
        .on("value", snapshot => (this.seats = snapshot.val()));
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
    valiateResponse(error, commited, snapshot) {
      if (error) {
        this.$notify({
          group: "foo",
          type: "error",
          title: "Error 😬",
          text: "Something happened, the operation couldn't complete"
        });
      }
      if (commited) {
        this.$notify({
          group: "foo",
          type: "success",
          title: "Great 😉",
          text: "Seats acquired successfully"
        });
        this.seats = snapshot.val();
      }
    },
    saveChanges() {
      // this.updateSelectedSteats();
      // this.updateSeats();
      firebase
        .database()
        .ref("rooms")
        .child("1")
        .transaction(
          storedValues => this.validateUpdateChanges(storedValues),
          (error, commited, snapshot) =>
            this.valiateResponse(error, commited, snapshot)
        );
      this.counter = 0;
    },
    validateUpdateChanges(values) {
      this.selectedSeats().forEach(seat => {
        if (values.find(s => s.id === seat.id).adquirid) {
          return;
        }
        seat.adquirid = true;
      });
      return this.seats;
    },
    selectedSeats() {
      return this.seats.filter(seat => !seat.avaliable && !seat.adquirid);
    },
    updateSelectedSteats() {
      this.selectedSeats().forEach(seat => (seat.adquirid = true));
    },
    release() {
      this.seats.forEach(seat => {
        seat.adquirid = false;
        seat.avaliable = true;
        seat.user_id = null;
      });
      this.updateSeats();
      this.counter = 0;
    },
    discardChanges() {
      this.counter = 0;
      this.selectedSeats().forEach(seat => {
        seat.user_id = null;
        seat.avaliable = true;
      });

      this.updateSeats();
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
.b-container {
  margin-top: 5px;
}
.rounded {
  border-radius: 50px !important;
}
</style>