<template>
  <div
    class="col seat mb-3"
    @click="selectSeat(seat.id)"
    :class="{'avaliable': seat.avaliable, 'not-avaliable': !seat.avaliable , 'pointer':seatAvaliable(seat)}"
  >
    <div class="text-center">
      <div class="mt-2">{{ seat.row }}{{ seat.number}}</div>

      <img src="../assets/cinema-chair.png" alt="cinema-chair" width="32px" class="m-0" />
    </div>
  </div>
</template>

<script>

export default {
  props: {
    seat: Object,
    user: String
  },
  methods: {
    selectSeat(id) {
      this.$emit("selectSeat", id);
    },
    seatAvaliable (seat) {
      return !seat.adquirid && (seat.user_id == null || seat.user_id == this.user)
    }
  }
};
</script>

<style scoped>
.seat {
  color: wihite;
  margin: 8px;
  font-weight: bold;
  border-radius: 3px;
}
.avaliable{
  background-color: rgba(18, 164, 231, 0.897);
}
.not-avaliable{
  background-color: rgba(156, 111, 189, 0.733);
}
.pointer{
  cursor: pointer;
}
</style>
