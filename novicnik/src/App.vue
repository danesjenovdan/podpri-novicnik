<script setup>
import Newsletter from "./components/Newsletter.vue";
</script>

<template>
  <Newsletter
    donation-campaign-id="4"
    email-label="Daj nam svoj mail, da te obveščamo!"
    email-placeholder="nekdo@email.si"
    checkbox-label="Strinjam se s pogoji in te zadeve."
    button-text="POŠLJI!"
    on-subscription-success="Hvala!"
    on-subscription-error="Žal ni uspelo :/"
  />
</template>
