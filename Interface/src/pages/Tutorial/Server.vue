<template>
  <div :class="{ mobile: isMobilePage }">
    <h1>{{ $t("tutorial.server.title") }}</h1>
    <slot></slot>
    <div id="card-with-navigation">
      <!-- TODO: these should be extract somehow, to be have the same functionality on the device part of the tutorial and here automatically -->
      <div class="navigation-circle back" v-if="!isMobilePage">
        <router-link :to="{ name: steps[activeIndex + -1] }">
          <img src="/arrow-thick-left.svg" />
        </router-link>

        <span>{{ $t("global.navigation.back") }}</span>
      </div>
      <Card :padded="!isMobilePage" id="server-card">
        <div v-if="activeIndex == 1" id="questionaire">
          <h2>{{ $t("tutorial.server.question") }}</h2>
          <div class="buttons">
            <router-link :to="{ name: 'tutorial-choose-server' }">
              <Button id="no" empty uppercase tabindex="2">
                {{
                $t("tutorial.server.no")
                }}
              </Button>
            </router-link>
            <router-link :to="{ name: 'tutorial-server-ip' }">
              <Button id="yes" empty uppercase tabindex="1">
                {{
                $t("tutorial.server.yes")
                }}
              </Button>
            </router-link>
          </div>
        </div>

        <div v-if="activeIndex == 2" id="ip">
          <h2>{{ $t("tutorial.server.ip") }}</h2>
          <IPInput @change="updateIp" />
          <a
            href="/docs/faq"
            target="_blank"
            rel="noopener noreferrer"
          >{{ $t("tutorial.server.whatsAnIP") }}</a>
        </div>
        <div v-if="activeIndex == 3" id="port">
          <h2>{{ $t("tutorial.server.port") }}</h2>
          <PortInput @change="updatePort" />
          <a
            href="/docs/faq"
            target="_blank"
            rel="noopener noreferrer"
          >{{ $t("tutorial.server.whatsAPort") }}</a>
        </div>
      </Card>
      <div class="navigation-circle next" v-if="!isMobilePage">
        <router-link :to="{ name: steps[activeIndex + 1] }">
          <img src="/arrow-thick-right.svg" />
        </router-link>
        <span>{{ $t("global.navigation.next") }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import Button from "../../components/Button";
import Card from "../../components/Card";
import IPInput from "../../components/IPInput";
import PortInput from "../../components/PortInput";

export default {
  components: { Button, Card, IPInput, PortInput },
  data() {
    return {
      ip: [undefined, undefined, undefined, undefined],
      port: undefined,
      steps: {
        0: "tutorial-welcome",
        1: "tutorial-server-questionaire",
        2: "tutorial-server-ip",
        3: "tutorial-server-port",
        4: "tutorial-device-form"
      }
    };
  },
  computed: {
    activeIndex() {
      return this.$route.meta.activeIndex;
    },
    isMobilePage() {
      return this.$store.state.websiteBeingViewedOnMobileDevice;
    }
  },
  methods: {
    updateIp({ ip, valid }) {
      if (valid) {
        this.ip = ip;
        this.save();
        this.$router.push({ name: "tutorial-server-port" });
      }
    },
    updatePort({ port, valid }) {
      if (valid) {
        this.port = port;
        this.save();
        this.$router.push({ name: "tutorial-device-form" });
      }
    },
    save() {
      // Checks for existence have already been done in the udpate methods
      const server = {
        ip: { v4: this.ip },
        port: this.port
      };

      this.$store.dispatch("updateServer", server);
    }
  }
};
</script>

<style scoped lang="scss">
h1 {
  text-align: center;
}
#card-with-navigation {
  margin-top: calc(#{$spacing-large} * 2);
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;

  & .navigation-circle {
    &.next {
      margin-left: $spacing-medium;
    }
    &.back {
      margin-right: $spacing-medium;
    }
    & a {
      display: flex;
      justify-content: center;
      align-items: center;
      width: $spacing-large;
      height: $spacing-large;
      border-radius: $spacing-large;
      @include box-shadow-light;

      color: $black;
      text-decoration: none;
      & img {
        height: $spacing-small * 3;
      }
    }
    & span {
      display: block;
      margin-top: $spacing-medium;
    }
  }
}
#server-card {
  // 100% - the navigation knobs
  width: calc(100% - (#{$spacing-medium} + #{$spacing-large}) * 2);
  min-height: 20vh;

  & h2 {
    text-align: center;
  }
  & .buttons {
    display: flex;
    justify-content: space-around;

    & .button {
      max-width: 20vw;
      display: flex;
      justify-content: center;
      flex: 1;

      &:first-child {
        margin-right: $spacing-medium;
      }
    }
  }
}

.mobile {
  & #server-card {
    // no navigation knobs on the side
    width: 100%;
    padding-top: $spacing-medium;
    padding-bottom: $spacing-medium;
  }
}

#ip {
  display: flex;
  flex-direction: column;
  align-items: center;

  & #ip-inputs {
    display: inline-flex;

    & input {
      min-width: 0;
      max-width: 5rem;
    }
  }

  a {
    margin-top: $spacing-large;
    text-align: center;
  }
}

#port {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

  & input {
    flex-grow: 0;
    margin-top: $spacing-large;
    min-width: 10rem;
  }

  & a {
    margin-top: calc(#{$spacing-large} * 2);
  }
}

.mobile {
}
</style>
