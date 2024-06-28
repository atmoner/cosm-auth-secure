<script>
import { RouterLink, RouterView } from 'vue-router'
 
import {
  AndroidBiometryStrength,
  BiometryError,
  BiometryErrorType,
  BiometricAuth
} from '@aparajita/capacitor-biometric-auth'


export default {
  data: function () {
    return {
      debugBiometry: "",
      errorBiometry: "",
    }
  },
  async mounted() {
    this.updateBiometryInfo(await BiometricAuth.checkBiometry())
    await BiometricAuth.setBiometryType(['faceAuthentication', 'fingerprintAuthentication'])
    await BiometricAuth.setBiometryIsEnrolled(true) 

    

    console.log(await BiometricAuth.checkBiometry())
    this.debugBiometry = JSON.stringify(await BiometricAuth.checkBiometry())
    try {
      let appListener = await BiometricAuth.addResumeListener(this.updateBiometryInfo)
      console.log(appListener)
    } catch (error) {
      if (error instanceof Error) {
        console.error(error.message)
      }
    }


    try {
    await BiometricAuth.authenticate({
      reason: 'Please authenticate',
      cancelTitle: 'Cancel',
      allowDeviceCredential: true,
      iosFallbackTitle: 'Use passcode',
      androidTitle: 'Biometric login',
      androidSubtitle: 'Log in using biometric authentication',
      androidConfirmationRequired: false,
      androidBiometryStrength: AndroidBiometryStrength.weak,
    })
  } catch (error) {
    // error is always an instance of BiometryError.
    if (error instanceof BiometryError) {
      if (error.code !== BiometryErrorType.userCancel) {
        // Display the error.
        await showAlert(error.message)
      }
    }
  }
  },
  methods: {
    updateBiometryInfo(info) {
      if (info.isAvailable) {
        console.log('Biometry is available')
        console.log(info.biometryType)
        // Biometry is available, info.biometryType will tell you the primary type.
      } else {
        // Biometry is not available, info.reason and info.code will tell you why.
      }
    },
    async checkBiometry() {
      try {
        updateBiometryInfo(await BiometricAuth.checkBiometry())
      } catch (error) {
        if (error instanceof Error) {
          console.error(error.message)
        }
      }
    },
    async authenticateBiometry() {
      try {
        updateBiometryInfo(await BiometricAuth.authenticateBiometry())
      } catch (error) {
        if (error instanceof Error) {
          console.error(error.message)
        }
      }
    },
    async authenticateBiometryWithCustomPrompt() {
      try {
        updateBiometryInfo(
          await BiometricAuth.authenticateBiometry({
            promptMessage: 'Authenticate with your biometry',
            cancelButtonText: 'Cancel',
          })
        )
      } catch (error) {
        if (error instanceof Error) {
          console.error(error.message)
        }
      }
    },
    async authenticateBiometryWithCustomPromptAndCustomErrors() {
      try {
        updateBiometryInfo(
          await BiometricAuth.authenticateBiometry({
            promptMessage: 'Authenticate with your biometry',
            cancelButtonText: 'Cancel',
            errors: {
              [BiometryErrorType.BIOMETRY_LOCKOUT]: 'Biometry lockout',
              [BiometryErrorType.BIOMETRY_NOT_ENROLLED]: 'Biometry not enrolled',
              [BiometryErrorType.BIOMETRY_NOT_SUPPORTED]: 'Biometry not supported',
              [BiometryErrorType.BIOMETRY_PERMISSION_DENIED]: 'Biometry permission denied',
              [BiometryErrorType.BIOMETRY_SCREEN_GUARD]: 'Biometry screen guard',
              [BiometryErrorType.BIOMETRY_UNAVAILABLE]: 'Biometry unavailable',
              [BiometryErrorType.BIOMETRY_UNKNOWN_ERROR]: 'Biometry unknown error',
            },
          })
        )
      } catch (error) {
        if (error instanceof Error) {
          console.error(error.message)
        }
      }
    },
  },
}
</script>


<template>
  <header>
    <img alt="Vue logo" class="logo" src="https://cdn-icons-png.freepik.com/512/1489/1489588.png" width="125" height="125" />

    <div class="wrapper">
 

      <nav>
        <!-- <RouterLink to="/">Home</RouterLink> -->
        <!-- <RouterLink to="/debug">Debug</RouterLink> -->
      </nav>
    </div>
  </header>
  <pre>{{ debugBiometry }}</pre>
  <!-- <RouterView /> -->
</template>

<style scoped>
pre {
    white-space: pre-line;
    word-wrap: break-word;
}
header {
  line-height: 1.5;
  max-height: 100vh;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
