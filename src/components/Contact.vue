<template>
  <div class="container-fluid py-md-5" id="contact">
    <div class="row justify-content-center py-md-0 my-md-3">
      <h2 class="text-center">Contact</h2>

      <div class="row g-0 contact-section">

        <!-- Map -->
        <div class="col-md-6 ms-auto my-5 my-md-0">
          <div id="map" class="text-center py-5">
            <iframe 
              src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3861.6689316139905!2d121.01345977587414!3d14.560915278045501!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x3397c909654a740d%3A0x8ac6a5f4e8f43afb!2sRCBC%20Plaza!5e0!3m2!1sen!2sph!4v1751286011555!5m2!1sen!2sph" 
              allowfullscreen loading="lazy" referrerpolicy="no-referrer-when-downgrade" title="Google map location of RCBC Plaza">
            </iframe>
          </div>
        </div>

        <!-- Contact Form -->
        <div class="col-md-6 d-flex justify-content-center align-items-center">
          <form @submit.prevent="submitForm">

            <div class="mb-3">
              <input type="text" class="form-control" v-model="name" placeholder="First Name M.I Last Name"/>
            </div>

            <div class="mb-3">
              <input type="email" class="form-control" v-model="email" placeholder="Email"/>
            </div>

            <div class="mb-3">
              <textarea class="form-control" v-model="message" rows="5" cols="50" placeholder="Your message"></textarea>
            </div>
            
            <div class="mb-3">
              <div ref="recaptchaContainer"></div>
            </div>
        
            
            <button type="submit" class="btn ms-auto" :disabled="isLoading">
              {{isLoading? "Sending..." : "Submit"}}
            </button>

          </form>
        </div>

        <!-- Social Icons -->
        <div class="social-icons">
          <a href="https://www.facebook.com/" target="_blank" rel="noopener noreferrer">
            <img src="/images/facebook.png" alt="facebook">
          </a>

          <a href="https://www.linkedin.com/home" target="_blank" rel="noopener noreferrer">
            <img src="/images/LinkedIn.png" alt="LinkedIn">
          </a>

          <a href="https://github.com/" target="_blank" rel="noopener noreferrer">
            <img src="/images/GitHub.png" alt="GitHub">
          </a>
        </div>
        <div class="text-center">
          <a href="#top"><h6>Back to Top</h6></a>
        </div>

      </div>
    </div>
  </div>
</template>



<script setup>

    import { ref, onMounted, onBeforeUnmount } from "vue";

    import { Notyf } from "notyf";
    import 'notyf/notyf.min.css';

    const notyf = new Notyf();



const WEB3FORMS_ACCESS_KEY = "5183685e-c01d-4bfd-8dc6-c2bea5f6ea64";
    // Add the web3form access key here
    
    const subject = "New message from Portfolio Contact Form";

    const name = ref("")
    const email = ref("")
    const message = ref("")

    const isLoading = ref(false);

    const submitForm = async () => {

        if(!recaptchaToken.value) {
            notyf.error('Please verify that you are not a robot.')
            return;
        }

        isLoading.value = true;
        try {
            const response = await fetch("https://api.web3forms.com/submit", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    Accept: "application/json",
                },
                body: JSON.stringify({
                    access_key: WEB3FORMS_ACCESS_KEY,
                    subject: subject,
                    name: name.value,
                    email: email.value,
                    message: message.value
                }),
            });
            const result = await response.json();

            if (result.success){
                console.log(result);
                isLoading.value = false;
                notyf.success("Message Sent!")
            }
        } catch (error) {
            console.log(error)
            isLoading.value = false;
            notyf.error("Failed to send message");
        } finally {
            //Reset captcha after submit or error
            resetRecaptcha()
        }
    }


   const SITE_KEY ='6Lf2idwrAAAAAELGyq7hV3JiUWMFvG-jW2qy24Q7'

    const recaptchaContainer = ref(null);
    const recaptchaWidgetId = ref(null);
    const recaptchaToken = ref('')

    function onRecaptchaSuccess(token) {
        recaptchaToken.value = token;
    }

    function onRecaptchaExpired() {
        recaptchaToken.value = '';
    }

    function renderRecaptcha() {
        if(!window.grecaptcha) {
            console.error('reCAPTCHA not loaded');
            return
        }

        recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
            sitekey: SITE_KEY,
            size: 'normal', //compact
            callback: onRecaptchaSuccess,
            'expired-callback': onRecaptchaExpired,
        });
    }

    //Function to reset reCaptcha

    function resetRecaptcha() {
        if (recaptchaWidgetId.value !== null) {
            window.grecaptcha.reset(recaptchaWidgetId.value)
            recaptchaToken.value = '';
        }
    }

    onMounted(() => {
        const interval = setInterval(() => {
            if(window.grecaptcha && window.grecaptcha.render) {
                renderRecaptcha();
                clearInterval(interval)
            }
        }, 100)

        onBeforeUnmount(() => {
            clearInterval(interval);
        })
    })
</script>