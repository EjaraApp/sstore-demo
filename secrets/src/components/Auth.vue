<template>
  <div>
    <b-form @submit="onSubmit">
      <b-form-group id="input-username" label="User Name:" label-for="username" description>
        <b-form-input id="username" v-model="form.username" type="text" required placeholder></b-form-input>
      </b-form-group>

      <b-form-group id="input-password" label="Password:" label-for="password">
        <b-form-input id="password" v-model="form.password" type="password" required placeholder></b-form-input>
      </b-form-group>

      <b-form-group id="input-confirm" label="Confirm Password:" label-for="confirm">
        <b-form-input id="confirm" v-model="form.confirm" type="password" required placeholder></b-form-input>
      </b-form-group>

      <b-button type="submit" variant="primary">Submit</b-button>
    </b-form>
    <ul style="color: red; margin-top: 20px">
      <li>Use at least 8 characters for the username (important).</li>
      <li>Use a strong password, at least 8 characters. </li>
      <li>Since this is just a demo we don't strongly insist but always use a strong password anytime for anything. </li>
    </ul>
  </div>
</template>

<script>
import { mapMutations } from "vuex";
export default {
  data() {
    return {
      form: {
        username: "",
        password: "",
        confirm: ""
      }
    };
  },
  methods: {
    ...mapMutations(["set_auth"]),
    onSubmit(evt) {
      evt.preventDefault();
      console.log(JSON.stringify(this.form));
      if (this.form.password !== this.form.confirm) {
        alert("Passwords Dont Match.");
        return false;
      }

      if(this.form.password.length < 8){
        alert("Passwords must be at least 8 characters.");
        return false;
      }

      if(this.form.username.length <8){
        alert("Username must be at least 8 characters.");
        return false;
      }
      
      // Generate private_key username and password

      this.$store.state.msg = 'Generating secret key from credentials, this could take a minute or three. ...';
      this.$store.state.show = true;
      this.$toKey(this.form.password.trim(), this.form.username.trim())
        .then(h => {
          const auth = {
            private_key: h.hashHex,
            username: this.form.username,
            password: this.form.password
          };

          this.$store.state.show = false;
          this.set_auth(auth);
          this.$bvToast.toast(
            "Secret Key Generation Done. Now you can perform the other operations.",
            {
              title: "Generating secret key from password  ...",
              variant: "success",
              solid: true
            }
          );
        })
        .catch((e) => {
          this.$bvToast.toast("something went wrong :(.", {
            title: "Generating secret key from password  ...",
            variant: "danger",
            solid: true
          });
          console.log(e);
        });
    }
  }
};
</script>


<style scoped>
</style>
