<template>
  <div action="" class="mb-16">
    <div class="relative">
      <div :class="isSended ? 'opacity-0 ' : ''">
        <h2 class="text-3xl font-bold">Drop me a line</h2>
        <form
          @submit.prevent="handleSubmit"
          netlify
          class="mt-8 space-y-4"
          ref="contactForm"
          method="POST"
          name="contact-form"
          v-if="showForm"
        >
          <input type="hidden" name="contact-form" value="name_of_my_form" />
          <input
            name="bot-field"
            class="hidden"
            type="text"
            v-model="botField"
          />

          <div class="w-full">
            <label for="name" class="sr-only">Name</label>
            <input
              type="text"
              name="name"
              placeholder="Name"
              class="input"
              required
              v-model="name"
            />
          </div>

          <div class="w-full">
            <label for="email" class="sr-only">Email</label>
            <input
              type="email"
              name="email"
              placeholder="Email"
              class="input"
              required
              v-model="email"
            />
          </div>
          <div class="w-full">
            <label for="message" class="sr-only">Message</label>
            <textarea
              name="message"
              placeholder="Message"
              class="input"
              required
              v-model="message"
            ></textarea>
          </div>
          <div class="">
            <button
              type="submit"
              class="
                font-medium
                w-full
                h-full
                py-3
                rounded-md
                shadow-md
                hover:shadow-lg
                text-white
                bg-teal-600
                dark:bg-teal-700
                hover:bg-teal-700
                dark:hover:bg-teal-800
                transition-all
                duration-150
                focus:outline-none
                focus:ring-4 focus:ring-teal-400
                dark:focus:ring-teal-500
                disable:opacity-70
              "
              :class="isSending ? 'opacity-70' : ''"
              :disabled="isSending"
            >
              {{ isSending ? "Sending" : "Send" }}
            </button>
          </div>
          <div class="relative">
            <transition name="fade-from-bottom">
              <p
                v-show="sendError"
                class="
                  absolute
                  w-full
                  top-0
                  left-0
                  text-center text-sm
                  font-medium
                  text-red-500
                  dark:text-red-400
                "
              >
                Something just happen, try again later.
              </p>
            </transition>
          </div>
        </form>
      </div>
      <transition name="fade-from-bottom">
        <div
          v-if="isSended"
          class="
            absolute
            w-full
            h-full
            top-0
            left-0
            flex
            items-center
            justify-center
          "
        >
          <h3 class="text-xl font-bold text-center">
            Thanks, i will replay you back soon.
          </h3>
        </div>
      </transition>
    </div>
    <div class="hidden">
      <form
        method="POST"
        name="contact-form"
        data-netlify="true"
        netlify-honeypot="bot-field"
      >
        <input name="bot-field" type="text" />
        <input type="text" name="name" />
        <input type="email" name="email" />
        <textarea name="message"></textarea>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isSended: false,
      isSending: false,
      sendError: false,
      name:'',
      email: "",
      message: "",
      showForm: false,
      botField: "",
    };
  },
  methods: {
    async handleSubmit(evt) {
      try {
        const res = await fetch("/contact-form", {
          method: "POST",
          headers: { "Content-Type": "application/x-www-form-urlencoded" },
          body: this.encode({
            "form-name": "contact-form",
            name: this.name,
            email: this.email,
            message: this.message,
            botField: this.botField,
          }),
        });

        if (res.ok) {
          this.handleSuccess();
        } else {
          this.handleError();
        }
      } catch (error) {
        this.handleError();
      }
    },
    handleSuccess() {
      console.log("(cf) success");
      this.isSending = false;
      this.isSended = true;
    },
    handleError() {
      console.log("(cf) error");
      this.isSending = false;
      this.sendError = true;

      const TID = setTimeout(() => {
        clearTimeout(TID);
        this.sendError = false;
      }, 5e3);
    },
    encode(data) {
      return Object.keys(data)
        .map(
          (key) => encodeURIComponent(key) + "=" + encodeURIComponent(data[key])
        )
        .join("&");
    },
  },
  async mounted() {
    await new Promise((resolve) => {
      setTimeout(resolve, 1e3);
    });
    console.log("now form will be rendered");
    this.showForm = true;
  },
};
</script>

<style>
</style>