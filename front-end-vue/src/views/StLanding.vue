<template>
  <div id="app">
    <span class="bg"></span>
    <v-app id="inspire">
      <v-row justify="center" class="mt-5" v-if="!su && !member">
        <v-dialog v-model="dialog" persistent max-width="600px">
          <template v-slot:activator="{ on, attrs }">
            <v-btn v-bind="attrs" v-on="on" color="#B2EBF2">
              <span style="color: #000 !important;">USER PROFILE</span>
            </v-btn>
          </template>
          <v-card >
            <v-card-title>
              <span class="headline">User Profile</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-form v-model="isValid">
                <v-row>
                  <v-col cols="6">
                    <v-text-field label="Phone No.*" v-model="phone" :rules="phoneRules" required></v-text-field>
                  </v-col>
                  <v-col cols="6">
                    <v-text-field label="Roll No.*" v-model="roll" :rules="rollRules" required></v-text-field>
                  </v-col>
                </v-row>
                </v-form>
              </v-container>
              <small>*indicates required field</small>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="dialog = false">Close</v-btn>
              <v-btn color="blue darken-1" text @click="setProfile"  :disabled="!isValid">Submit</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-row>
      <div
        class="login-box"
        v-if="(((student.studentround >= audition.round) && (audition.status != 'res'))  || su || member)"
      >
        <v-row align="center" justify="center" v-if="audition.round != 0">
          <v-col class="text-center" cols="12">
            <h4 class="glitch">ROUND {{audition.round}}</h4>
            <p class="glitch" v-if="audition.status === 'res'">
              The results are out now
              <v-btn @click="$router.push('/results')" color="#B2EBF2" style="margin: 6px;">
                <span style="color: #000 !important;">Results</span>
              </v-btn>
            </p>
            <p v-if="audition.status === 'def'">The results will be out soon</p>
            <p v-if="audition.status === 'ong'">
              <v-btn
                @click="$router.push('/audition')"
                color="#B2EBF2"
                v-if="audition.status === 'ong' && profile.profilebool === true && (student.studenttime === 0 || su || member || (student.studenttime >0 && student.studenttime > (new Date())))"
                style="margin: 6px;"
              >
                <span style="color: #000 !important;">ATTEMPT</span>
              </v-btn>
            </p>
            <p class="glitch" v-if="profile.profilebool === false">COMPLETE YOUR PROFILE FIRST</p>

            <v-btn
              @click="$router.push('/admin')"
              v-if="member === true"
              color="#B2EBF2"
              style="margin: 6px;"
            >
              <span style="color: #000 !important;">MEMBER</span>
            </v-btn>
            <v-btn @click="$router.push('/root')" v-show="su" color="#B2EBF2" style="margin: 6px;">
              <span style="color: #000 !important;">ROOT</span>
            </v-btn>
            <v-btn @click="$router.push('/admin')" v-show="su" color="#B2EBF2" style="margin: 6px;">
              <span style="color: #000 !important;">DASHBOARD</span>
            </v-btn>
            <v-btn @click="logout" color="#B2EBF2" style="margin: 6px;">
              <span style="color: #000 !important;">Logout</span>
            </v-btn>
          </v-col>
        </v-row>
        <v-row align="center" justify="center" v-if="audition.round === 0">
          <v-col class="text-center" cols="12">
            <h1 class="glitch">START AUDITION PROCESS</h1>
            <p v-if="audition.status === 'res'">
              <v-btn @click="$router.push('/addround')" color="#B2EBF2" style="margin: 6px;">
                <span style="color: #000 !important;">ADD ROUND</span>
              </v-btn>
            </p>
            <p v-if="audition.status === 'def'">The results for {{audition.round}} will be out soon</p>
            <p v-if="audition.status === 'ong'">
              <v-btn
                @click="$router.push('/audition')"
                color="#B2EBF2"
                v-if="audition.status === 'ong'"
                style="margin: 6px;"
              >
                <span style="color: #000 !important;">ATTEMPT</span>
              </v-btn>
            </p>

            <v-btn
              @click="$router.push('/admin')"
              v-if="member === true"
              color="#B2EBF2"
              style="margin: 6px;"
            >
              <span style="color: #000 !important;">MEMBER</span>
            </v-btn>
            <v-btn @click="$router.push('/root')" v-if="su" color="#B2EBF2" style="margin: 6px;">
              <span style="color: #000 !important;">ROOT</span>
            </v-btn>
            <v-btn @click="$router.push('/admin')" v-show="su" color="#B2EBF2" style="margin: 6px;">
              <span style="color: #000 !important;">DASHBOARD</span>
            </v-btn>
            <v-btn @click="logout" color="#B2EBF2" style="margin: 6px;">
              <span style="color: #000 !important;">Logout</span>
            </v-btn>
          </v-col>
        </v-row>
      </div>
      <div class="login-box" v-if="((audition.round === 0) && (!su && !member))">
        <v-row align="center" justify="center">
          <v-col class="text-center" cols="12">
            <h1 class="glitch">AUDITIONS WILL START SOON</h1>
            <v-btn @click="logout" color="#B2EBF2" style="margin: 6px;">
              <span style="color: #000 !important;">Logout</span>
            </v-btn>
          </v-col>
        </v-row>
      </div>
      <div
        class="login-box"
        v-if="(((student.studentround < audition.round) || ((student.studentround === audition.round) && (audition.status === 'res'))) && (!su && !member))"
      >
        <v-row align="center" justify="center">
          <v-col class="text-center" cols="12">
            <h1 class="glitch">THANK YOU FOR YOUR PARTICIPATION</h1>
            <h4 class="glitch">WE HOPE TO SEE YOU IN FURTHER GLUG EVENTS</h4>
            <p v-if="((audition.status === 'res') && (audition.round != 0))" class="glitch">
              The results for {{audition.round}} are out now
              <v-btn @click="$router.push('/results')" color="#B2EBF2" style="margin: 6px;">
                <span style="color: #000 !important;">Results</span>
              </v-btn>
            </p>
          </v-col>
        </v-row>
      </div>
      <div
        class="login-box"
        v-if="((student.studentround > audition.round) && ((audition.status === 'res')) && (!su && !member)) &&(audition.round != 0)"
      >
        <v-row align="center" justify="center">
          <v-col class="text-center" cols="12">
            <h1 class="glitch">The results for {{audition.round}} are out now</h1>
            <v-btn color="#B2EBF2" style="margin: 6px;" @click="$router.push('/results')">
              <span style="color: #000 !important;">Results</span>
            </v-btn>
            <v-btn @click="logout" color="#B2EBF2" style="margin: 6px;">
              <span style="color: #000 !important;">Logout</span>
            </v-btn>
          </v-col>
        </v-row>
      </div>
    </v-app>
  </div>
</template>

<script>
import common from "@/services/common.js";
import VueJwtDecode from "vue-jwt-decode";

export default {
  components: {},
  data: () => ({
    member: false,
    su: false,
    audition: [],
    student: null,
    dialog: false,
    profile: [],
    phone: "",
    roll: "",
    isValid: true,
    phoneRules: [
      v => !!v || "Phone No is required",
      v => /^-?(\d+\.?\d*)$|(\d*\.?\d+)$/.test(v) || "Phone no must be valid",
      v =>
        (v && v.length <= 10 && v.length >= 10) ||
        "Enter a valid (91+) 10 digit Phone no"
    ],
    rollRules: [
      v => !!v || "Roll No is required",
      v => /^([a-zA-Z0-9-]+)$/.test(v) || "Roll No must be valid",
      v =>
        (v && v.length <= 10 ) ||
        "Enter a valid Roll no"
    ]
  }),
  name: "Landing",
  beforeCreate() {
    common.getStudent().then(res => {
      this.student = res.data;
    });
    common.getAuditionStatus().then(res => {
      this.audition = res.data;
    });
    common.getProfile().then(res => {
      this.profile = res.data;
      this.phone = this.profile.phone;
      this.roll = this.profile.roll;
      console.log(this.profile);
    });
    if (localStorage.getItem("token") === null) {
      this.$router.push("/");
    }
  },

  created() {
    this.$vuetify.theme.dark = true;
    if (localStorage.getItem("token") === null) {
      this.$router.push("/");
    } else {
      var tok = VueJwtDecode.decode(localStorage.getItem("token").substring(6));
      if (tok.role === "m") {
        this.member = true;
      } else if (tok.role === "su") {
        this.su = true;
      }
    }
  },
  methods: {
    logout() {
      // eslint-disable-next-line no-unused-vars
      common.logout().then(res => {
        localStorage.clear();
        this.$router.push("/login");
      });
    },
    startCallBack: function(x) {
      console.log(x);
    },
    endCallBack: function(x) {
      console.log(x);
    },
    setProfile() {
      common.setProfile({ roll: this.roll, phone: this.phone }).then(() => {
        this.dialog = false;
        this.profile.profilebool = true;
      });
    }
  }
};
</script>

<style scoped>
.bg {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  background: url("../assets/img/f.gif") no-repeat center center;
  background-size: cover;
  filter: blur(0px);
  background-color: rgba(0, 0, 0, 0.5);
  opacity: 0.7;
}
.nav-wrap {
  height: 10vh;
}
.login-box {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 90%;
  max-width: 400px;
  padding: 20px;
  transform: translate(-50%, -50%);
  backdrop-filter: blur(9px);
  background-color: rgba(255, 255, 255, 0.05);
  border-radius: 20px;
  box-shadow: 0px 0px 40px rgb(0, 2, 26);
  border: 0px solid rgb(1, 0, 46);
  border-bottom: 8px solid rgb(161, 183, 255);
}
.rules {
  color: #fff;
  width: 90%;
  max-width: 400px;
  border-radius: 20px;
  border: 1px solid #fff;
  font-size: 14px;
  margin: 0 auto;
  padding: 10px;
}

.glitch {
  font-size: 1.7rem;
  font-weight: 700;
  text-transform: uppercase;
  position: relative;
  letter-spacing: 3px;
  text-shadow: 0.05em 0 0 rgba(255, 0, 0, 0.75),
    -0.025em -0.05em 0 rgba(0, 255, 0, 0.75),
    0.025em 0.05em 0 rgba(0, 0, 255, 0.75);

  animation: glitch 500ms infinite;
}

@keyframes glitch {
  0% {
    text-shadow: 0.05em 0 0 rgba(255, 0, 0, 0.75),
      -0.05em -0.025em 0 rgba(0, 255, 0, 0.75),
      -0.025em 0.05em 0 rgba(0, 0, 255, 0.75);
  }
  14% {
    text-shadow: 0.05em 0 0 rgba(255, 0, 0, 0.75),
      -0.05em -0.025em 0 rgba(0, 255, 0, 0.75),
      -0.025em 0.05em 0 rgba(0, 0, 255, 0.75);
  }
  15% {
    text-shadow: -0.05em -0.025em 0 rgba(255, 0, 0, 0.75),
      0.025em 0.025em 0 rgba(0, 255, 0, 0.75),
      -0.05em -0.05em 0 rgba(0, 0, 255, 0.75);
  }
  49% {
    text-shadow: -0.05em -0.025em 0 rgba(255, 0, 0, 0.75),
      0.025em 0.025em 0 rgba(0, 255, 0, 0.75),
      -0.05em -0.05em 0 rgba(0, 0, 255, 0.75);
  }
  50% {
    text-shadow: 0.025em 0.05em 0 rgba(255, 0, 0, 0.75),
      0.05em 0 0 rgba(0, 255, 0, 0.75), 0 -0.05em 0 rgba(0, 0, 255, 0.75);
  }
  99% {
    text-shadow: 0.025em 0.05em 0 rgba(255, 0, 0, 0.75),
      0.05em 0 0 rgba(0, 255, 0, 0.75), 0 -0.05em 0 rgba(0, 0, 255, 0.75);
  }
  100% {
    text-shadow: -0.025em 0 0 rgba(255, 0, 0, 0.75),
      -0.025em -0.025em 0 rgba(0, 255, 0, 0.75),
      -0.025em -0.05em 0 rgba(0, 0, 255, 0.75);
  }
}

.question_cont {
  margin-top: 5rem !important;
}

@media only screen and (max-width: 576px) {
  .question_cont {
    margin-top: 3rem !important;
  }
}
</style>
