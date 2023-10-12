<template>
    <span class="fa-stack fa-4x" style="font-size:50px;">
        <img :src="img_src" :alt="img_alt" style="width: 80px" />
    </span>

    <div>
        <h4 class="my-3"> {{card_title}} </h4> 
    </div>

    <p class="text-muted">
        {{card_text}}
    </p>

    <button class="btn btn-primary">click here to learn more</button>
    <div class="text-read"> <span class="my-2" v-if="readed"><img src="@/assets/img/read.svg" style="width:24px;"></span></div>
</template>

<script setup>

const props = defineProps({ img_src: String,
                            img_alt: String,
                            card_text: String,
                            card_title: String,
                            readed: Boolean })

// const changeReadedStatus = () => {
//     readed.value = true;
// }

</script>

<style>
.fa-stack {
  width: 2em;
  height: 2em;
  background: #ffc800;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0 auto;
}
.text-read{
    position: absolute;
    left:50%;
    bottom:12px;
    margin-left: -12px;
}
</style>