# FirebaseOTP_Integration
Authenticate with Firebase on Android using a Phone Number

Enable Phone Number sign-in for your Firebase project
To sign in users by SMS, you must first enable the Phone Number sign-in method for your Firebase project:

dependencies {
    // Import the BoM for the Firebase platform
    implementation platform('com.google.firebase:firebase-bom:26.7.0')

    // Declare the dependency for the Firebase Authentication library
    // When using the BoM, you don't specify versions in Firebase library dependencies
    implementation 'com.google.firebase:firebase-auth'
}

In the Firebase console, open the Authentication section.
On the Sign-in Method page, enable the Phone Number sign-in method.

To enable SafetyNet for use with Firebase Authentication: reCAPTCHA verification


Send a verification code to the user's phone

PhoneAuthOptions options = 
  PhoneAuthOptions.newBuilder(mAuth) 
      .setPhoneNumber(phoneNumber)       // Phone number to verify
      .setTimeout(60L, TimeUnit.SECONDS) // Timeout and unit
      .setActivity(this)                 // Activity (for callback binding)
      .setCallbacks(mCallbacks)          // OnVerificationStateChangedCallbacks
      .build();
  PhoneAuthProvider.verifyPhoneNumber(options);     
  
  auth.setLanguageCode("fr");
// To apply the default app language instead of explicitly setting it.
// auth.useAppLanguage();

mCallbacks = new PhoneAuthProvider.OnVerificationStateChangedCallbacks() {

    @Override
    public void onVerificationCompleted(PhoneAuthCredential credential) {
        // This callback will be invoked in two situations:
        // 1 - Instant verification. In some cases the phone number can be instantly
        //     verified without needing to send or enter a verification code.
        // 2 - Auto-retrieval. On some devices Google Play services can automatically
        //     detect the incoming verification SMS and perform verification without
        //     user action.
        Log.d(TAG, "onVerificationCompleted:" + credential);

        signInWithPhoneAuthCredential(credential);
    }

    @Override
    public void onVerificationFailed(FirebaseException e) {
        // This callback is invoked in an invalid request for verification is made,
        // for instance if the the phone number format is not valid.
        Log.w(TAG, "onVerificationFailed", e);

        if (e instanceof FirebaseAuthInvalidCredentialsException) {
            // Invalid request
        } else if (e instanceof FirebaseTooManyRequestsException) {
            // The SMS quota for the project has been exceeded
        }

        // Show a message and update the UI
    }

    @Override
    public void onCodeSent(@NonNull String verificationId,
                           @NonNull PhoneAuthProvider.ForceResendingToken token) {
        // The SMS verification code has been sent to the provided phone number, we
        // now need to ask the user to enter the code and then construct a credential
        // by combining the code with a verification ID.
        Log.d(TAG, "onCodeSent:" + verificationId);

        // Save verification ID and resending token so we can use them later
        mVerificationId = verificationId;
        mResendToken = token;
    }
};
