<template>
  <div class="footer-container">
    <div class="row mb-1">
      <div class="col-12">
        <span class="title">Información de contacto </span>
      </div>
    </div>
    <div class="row">
      <div class="col-6 d-flex align-items-center">
        <MailIcon width="25" height="25" style="margin-right: 5px" />
        <a href="mailto:info@essencecreatives.com">info@essensecreatives.com</a>
      </div>
      <div class="col-6 d-flex align-items-center justify-content-end">
        <LocationIcon width="25" height="25" />
        <span>Tizimín, Yucatán, México</span>
      </div>
    </div>

    <div class="row mt-3">
      <div class="col-6 d-flex align-items-center">
        <CopyrightIcon width="15" height="15" style="margin-right: 5px" />
        <span> {{ currentYear }}. All rights reserved. </span>
      </div>
      <div
        class="col-6 d-flex align-items-center justify-content-end"
        style="gap: 0.5em"
      >
        <TwitterIcon width="25" height="25" />
        <LinkedinIcon width="25" height="25" />
        <InstagramIcon width="25" height="25" />
        <PinterestIcon width="25" height="25" />
      </div>
    </div>

    <div class="row">
      <div class="col-6">
        <span> Powered by Essence Creatives. </span>
      </div>
    </div>
  </div>
</template>
<script>
import LocationIcon from "./icons/LocationIcon.vue";
import MailIcon from "./icons/MailIcon.vue";
import CopyrightIcon from "./icons/CopyrightIcon.vue";
import TwitterIcon from "./icons/TwitterIcon.vue";
import LinkedinIcon from "./icons/LinkedinIcon.vue";
import InstagramIcon from "./icons/InstagramIcon.vue";
import PinterestIcon from "./icons/PinterestIcon.vue";
export default {
  components: {
    LocationIcon,
    MailIcon,
    CopyrightIcon,
    TwitterIcon,
    LinkedinIcon,
    InstagramIcon,
    PinterestIcon,
  },
  data: () => {
    return {
      currentYear: new Date().getFullYear(),
    };
  },
};
</script>
<style lang="scss" scoped>
.footer-container {
  background-color: var(--primary);
  width: 100%;
  padding: 15px;
  color: var(--white);

  bottom: 0;
}

.title {
  font-weight: bold;
}

a {
  color: var(--white);
  transition: none;
}
</style>
