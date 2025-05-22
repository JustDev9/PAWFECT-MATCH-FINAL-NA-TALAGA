<template>
  <div>
    <nav class="nav-container" :class="{ 'nav-scrolled': hasScrolled }">
      <div class="logo-container">
        <div class="logo-image">
          <img src="/Designer.png" alt="Pawfect" width="40" height="40">
        </div>
        <span class="logo-text">PAWFECT</span>
      </div>
      
      <div class="nav-links-container">
        <transition name="fade">
          <div v-if="mobileMenuOpen || !isMobile" class="nav-links" :class="{ 'mobile-active': mobileMenuOpen }">
            <a href="/home" class="nav-link" @click="closeMenuIfMobile">Home</a>
            <a href="/pet-profiles" class="nav-link" @click="closeMenuIfMobile">Pet Profiles</a>
            <div class="dropdown">
              <a href="#" class="nav-link dropdown-toggle" @click="toggleDesktopDropdown">
                Resources <span class="dropdown-arrow" :class="{ 'arrow-rotated': dropdownOpen }">▼</span>
              </a>
              <transition name="slide-fade">
                <div v-if="isMobile && dropdownOpen" class="dropdown-content mobile">
                  <a href="/training" @click="closeMenuIfMobile">Training Tips</a>
                  <a href="/stories" @click="closeMenuIfMobile">Success Stories</a>
                </div>
              </transition>
              <transition name="resources-dropdown">
                <div v-if="!isMobile && desktopDropdownOpen" class="dropdown-content desktop">
                  <a href="/training">Training Tips</a>
                  <a href="/stories">Success Stories</a>
                </div>
              </transition>
            </div>
            <a href="/donations" class="nav-link" @click="closeMenuIfMobile">Donation</a>
            <a href="/about" class="nav-link" @click="closeMenuIfMobile">About</a>
          </div>
        </transition>
      </div>
      
      <div class="right-section">
        <div class="user-dropdown">
          <div class="user-icon" role="button" aria-label="User profile" tabindex="0" @click="toggleUserDropdown">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path>
              <circle cx="12" cy="7" r="4"></circle>
            </svg>
          </div>
          
          <transition name="dropdown-animation">
            <div v-if="userDropdownOpen" class="user-dropdown-content" :class="{ 'mobile-dropdown': isMobile }">
              <div class="dropdown-header">
                <span>User Menu</span>
                <button class="close-dropdown-btn" @click="closeUserDropdown" aria-label="Close menu">
                  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                    <line x1="18" y1="6" x2="6" y2="18"></line>
                    <line x1="6" y1="6" x2="18" y2="18"></line>
                  </svg>
                </button>
              </div>
              <a href="/profile" @click.stop>Profile</a>
              <a href="/status" @click.stop>Status</a>
              <a href="/logout" @click.stop>Log Out</a>
            </div>
          </transition>
        </div>
        
        <button class="mobile-menu-toggle" aria-label="Toggle menu" @click="toggleMobileMenu">
          <div class="bar" :class="{ 'bar-1-active': mobileMenuOpen }"></div>
          <div class="bar" :class="{ 'bar-2-active': mobileMenuOpen }"></div>
          <div class="bar" :class="{ 'bar-3-active': mobileMenuOpen }"></div>
        </button>
      </div>
    </nav>

    <div class="pet-profile-container">
      <div class="pet-card">
        <div class="pet-image-container">
          <img src="" alt="Babi" class="pet-image" />
        </div>
        <div class="pet-info">
          <h2 class="pet-name">Hi! I'm Babi</h2>
          <div class="pet-details">
            <div class="detail-item">
              <span class="detail-label">Age:</span>
              <span class="detail-value">2</span>
            </div>
            <div class="detail-item">
              <span class="detail-label">Breed:</span>
              <span class="detail-value">Persian</span>
            </div>
            <div class="detail-item">
              <span class="detail-label">Sex:</span>
              <span class="detail-value">M</span>
            </div>
          </div>
          <p class="pet-description">
            Babi was found on a corner street in Olongapo and is trying to find his loved ones.
            He has a gullible and joyful personality and likes to play with other people.
            He enjoys petting and playtime.
          </p>
          <button class="adoption-button" @click="openApplicationForm">Apply for Adoption</button>
        </div>
      </div>
    </div>

    <div class="container adoption-process">
      <h2 class="title">Process of Adoption</h2>
      <div class="steps">
        <div class="step">
          <div class="circle">1</div>
          <p>Apply</p>
        </div>
        <div class="line"></div>
        <div class="step">
          <div class="circle">2</div>
          <p>Screening</p>
        </div>
        <div class="line"></div>
        <div class="step">
          <div class="circle">3</div>
          <p>Finalize Adoption</p>
        </div>
        <div class="line"></div>
        <div class="step">
          <div class="circle">4</div>
          <p>Bring Home</p>
        </div>
      </div>
    </div>

    <div>
    <div class="application-form-section">
      <div class="form-container" :class="{ 'show-form': showApplicationForm }">
        <div class="form-overlay" @click="closeApplicationForm"></div>
        <div class="form-content">
          <button class="close-form-btn" @click="closeApplicationForm">&times;</button>

          <h2 class="form-title">🐾 Pet Adoption Application Form</h2>
          <p class="form-description">
            Please fill out the form below to apply for adoption. All fields marked with * are required.
          </p>

          <form @submit.prevent="currentApplicationStep === 1 ? proceedToUploadsAndFinalize() : submitFullApplication()" class="adoption-form">
            
            <div v-if="currentApplicationStep === 1">
              <h3>1. Applicant Information</h3>
              <div class="form-group">
                <label for="fullName">Full Name *</label>
                <input type="text" id="fullName" v-model="application.fullName" required>
              </div>
              <div class="form-group">
                 <label for="ageOver18" class="checkbox-label">
                    <input type="checkbox" id="ageOver18" v-model="application.ageOver18" required>
                    I confirm I am 18 years or older *
                </label>
              </div>

              <!-- Guardian Information (displays only if user is under 18) -->
              <div v-if="!application.ageOver18" class="guardian-section">
                <h3>Guardian Information</h3>
                <div class="form-group">
                  <label for="guardianName">Guardian's Full Name *</label>
                  <input type="text" id="guardianName" v-model="application.guardianName" required>
                </div>
                <div class="form-group">
                  <label for="guardianRelationship">Relationship to Applicant *</label>
                  <input type="text" id="guardianRelationship" v-model="application.guardianRelationship" required>
                </div>
                <div class="input-row">
                  <div class="form-group">
                    <label for="guardianPhone">Guardian's Phone Number *</label>
                    <input type="tel" id="guardianPhone" v-model="application.guardianPhone" @input="validatePhoneInput($event, 'guardianPhone')" pattern="[0-9]*" required>
                  </div>
                  <div class="form-group">
                    <label for="guardianEmail">Guardian's Email Address *</label>
                    <input type="email" id="guardianEmail" v-model="application.guardianEmail" required>
                  </div>
                </div>
                <div class="form-group">
                  <label class="checkbox-label">
                    <input type="checkbox" v-model="application.guardianConsent" required>
                    My guardian is aware of and consents to this pet adoption application *
                  </label>
                </div>
              </div>

              <div class="form-group">
                <label>Address *</label>
                <input type="text" v-model="application.addressStreet" placeholder="Street Address" required>
                <div class="input-row">
                    <input type="text" v-model="application.addressCity" placeholder="City" required class="mt-small">
                    <input type="text" v-model="application.addressState" placeholder="Province" required class="mt-small">
                    <input type="text" v-model="application.addressZIP" placeholder="ZIP Code" required class="mt-small" @input="application.addressZIP = application.addressZIP.replace(/[^0-9]/g, '')">
                </div>
              </div>
              <div class="input-row">
                <div class="form-group">
                  <label for="phoneNumber">Phone Number *</label>
                  <input type="tel" id="phoneNumber" v-model="application.phoneNumber" @input="validatePhoneInput($event, 'phoneNumber')" pattern="[0-9]*" required>
                </div>
                <div class="form-group">
                  <label for="emailAddress">Email Address *</label>
                  <input type="email" id="emailAddress" v-model="application.emailAddress" required>
                </div>
              </div>

              <h3>2. Household Information</h3>
               <div class="form-group">
                <label>Do you own or rent your home? *</label>
                <div>
                    <label><input type="radio" v-model="application.homeOwnership" value="Own" required> Own</label>
                    <label><input type="radio" v-model="application.homeOwnership" value="Rent"> Rent</label>
                </div>
              </div>
              <div v-if="application.homeOwnership === 'Rent'" class="form-group">
                <label>If renting, do you have landlord's permission to have pets? *</label>
                 <div>
                    <label><input type="radio" v-model="application.landlordPermission" value="Yes" required> Yes</label>
                    <label><input type="radio" v-model="application.landlordPermission" value="No"> No</label>
                 </div>
              </div>
              <div class="form-group">
                <label for="homeType">Type of home: *</label>
                <select id="homeType" v-model="application.homeType" required>
                    <option value="House">House</option>
                    <option value="Apartment">Apartment</option>
                    <option value="Condo">Condo</option>
                    <option value="Other">Other</option>
                </select>
                 <input v-if="application.homeType === 'Other'" type="text" v-model="application.homeTypeOther" placeholder="Please specify" class="mt-small">
              </div>
              <div class="form-group">
                <label for="timeAtAddress">How long have you lived at this address? *</label>
                <input type="text" id="timeAtAddress" v-model="application.timeAtAddress" required>
              </div>

              <h3>3. Employment & Lifestyle</h3>
              <div class="form-group">
                <label for="occupation">Occupation *</label>
                <input type="text" id="occupation" v-model="application.occupation" required>
              </div>

              <div class="form-group">
                <label for="petCaregiverWhenNotHome">Who will care for the pet when you're not home or on vacation? *</label>
                <input type="text" id="petCaregiverWhenNotHome" v-model="application.petCaregiverWhenNotHome" required>
              </div>

              <h3>4. Care & Commitment</h3>
                <div class="form-group">
                    <label for="petSleepLocation">Where will the pet sleep? *</label>
                    <input type="text" id="petSleepLocation" v-model="application.petSleepLocation" required>
                </div>
                <div class="form-group">
                    <label for="petFood">What will you feed the pet? (e.g., brand, type of food) *</label>
                    <input type="text" id="petFood" v-model="application.petFood" required>
                </div>
                <div class="form-group">
                    <label>Are you prepared for the financial responsibility of pet ownership (food, vet care, emergencies, etc.)? *</label>
                    <div>
                        <label><input type="radio" v-model="application.preparedForFinancialResponsibility" :value="true" required> Yes</label>
                        <label><input type="radio" v-model="application.preparedForFinancialResponsibility" :value="false"> No</label>
                    </div>
                </div>

              <h3>5. Emergency & Long-Term Planning</h3>
                <div class="form-group">
                    <label for="emergencyPetCaregiver">Who will care for the pet if something happens to you? *</label>
                    <input type="text" id="emergencyPetCaregiver" v-model="application.emergencyPetCaregiver" required>
                </div>

                <div v-if="application.surrenderedPetBefore" class="form-group">
                    <label for="surrenderedPetExplanation">If yes, please explain the circumstances:</label>
                    <textarea id="surrenderedPetExplanation" v-model="application.surrenderedPetExplanation" rows="3"></textarea>
                </div>

              <h3>6. References (Optional)</h3>
                <p>Please provide 1-2 personal references.</p>
                <h4>Reference 1</h4>
                <div class="input-row">
                    <div class="form-group">
                        <label for="reference1Name">Name:</label>
                        <input type="text" id="reference1Name" v-model="application.reference1Name">
                    </div>
                    <div class="form-group">
                        <label for="reference1Relationship">Relationship:</label>
                        <input type="text" id="reference1Relationship" v-model="application.reference1Relationship">
                    </div>
                </div>
                <div class="input-row">
                    <div class="form-group">
                        <label for="reference1Phone">Phone Number:</label>
                        <input type="tel" id="reference1Phone" v-model="application.reference1Phone" @input="validatePhoneInput($event, 'reference1Phone')" pattern="[0-9]*">
                    </div>
                    <div class="form-group">
                        <label for="reference1Email">Email:</label>
                        <input type="email" id="reference1Email" v-model="application.reference1Email">
                    </div>
                </div>

                <h4>Reference 2</h4>
                <div class="input-row">
                    <div class="form-group">
                        <label for="reference2Name">Name:</label>
                        <input type="text" id="reference2Name" v-model="application.reference2Name">
                    </div>
                    <div class="form-group">
                        <label for="reference2Relationship">Relationship:</label>
                        <input type="text" id="reference2Relationship" v-model="application.reference2Relationship">
                    </div>
                </div>
                <div class="input-row">
                    <div class="form-group">
                        <label for="reference2Phone">Phone Number:</label>
                        <input type="tel" id="reference2Phone" v-model="application.reference2Phone" @input="validatePhoneInput($event, 'reference2Phone')" pattern="[0-9]*">
                    </div>
                    <div class="form-group">
                        <label for="reference2Email">Email:</label>
                        <input type="email" id="reference2Email" v-model="application.reference2Email">
                    </div>
                </div>

              <div class="form-actions">
                <button type="submit" class="submit-btn">Next: Upload Documents & Finalize</button>
              </div>
            </div>

            <div v-if="currentApplicationStep === 2">
              <h3>7. Upload Documents</h3>
              <div class="form-group">
                <label for="homePhoto">Upload a photo of your home or pet area: *</label>
                <input type="file" id="homePhoto" @change="handleFileUpload($event, 'homePhotoFile')" accept="image/*" required>
              </div>
              <div class="form-group">
                <label for="idPhoto">Upload a copy of your Valid ID: *</label>
                <input type="file" id="idPhoto" @change="handleFileUpload($event, 'idPhotoFile')" accept="image/*,application/pdf" required>
              </div>
              
              <!-- Guardian ID upload (displays only if user is under 18) -->
              <div v-if="!application.ageOver18" class="form-group">
                <label for="guardianIdPhoto">Upload a copy of Guardian's Valid ID: *</label>
                <input type="file" id="guardianIdPhoto" @change="handleFileUpload($event, 'guardianIdPhotoFile')" accept="image/*,application/pdf" required>
              </div>
              
              <h3>8. Acknowledgements & Consent</h3>
              <div class="form-group">
                <label class="checkbox-label">
                    <input type="checkbox" v-model="application.certifyTrueInfo" required>
                    I certify that all information provided in this application is true and complete to the best of my knowledge. *
                </label>
              </div>
              <div class="form-group">
                 <label class="checkbox-label">
                    <input type="checkbox" v-model="application.authorizeVerification" required>
                    I authorize the rescue/shelter to verify this information, including contacting references and veterinarians. *
                </label>
              </div>
              <div class="form-group">
                <label for="signature">Signature (Type Full Name) *</label>
                <input type="text" id="signature" v-model="application.signature" required>
              </div>
              
              <!-- Guardian signature (displays only if user is under 18) -->
              <div v-if="!application.ageOver18" class="form-group">
                <label for="guardianSignature">Guardian's Signature (Type Full Name) *</label>
                <input type="text" id="guardianSignature" v-model="application.guardianSignature" required>
              </div>
              
              <div class="form-group">
                <label for="applicationDate">Date *</label>
                <input type="date" id="applicationDate" v-model="application.applicationDate" required readonly>
              </div>

              <div class="form-actions">
                 <button type="button" class="submit-btn secondary-btn" @click="currentApplicationStep = 1">Back to Details</button>
                <button type="submit" class="submit-btn">Submit Application</button>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</div>
</template>

<script>
export default {
  name: 'PawfectAdoption',
  data() {
    return {
      // Navigation-related state
      mobileMenuOpen: false,
      dropdownOpen: false,         
      desktopDropdownOpen: false, 
      userDropdownOpen: false,     
      isMobile: false,
      hasScrolled: false,

      // Application form state
      showApplicationForm: false,
      currentApplicationStep: 1, 
      application: {
        // 1. Applicant Information
        fullName: '',
        ageOver18: false,
        addressStreet: '',
        addressCity: '',
        addressState: '',
        addressZIP: '',
        phoneNumber: '',
        emailAddress: '',

        // Guardian Information (when user is under 18)
        guardianName: '',
        guardianRelationship: '',
        guardianPhone: '',
        guardianEmail: '',
        guardianConsent: false,
        guardianSignature: '',

        // 2. Household Information
        homeOwnership: '', 
        landlordPermission: '', 
        homeType: 'House', 
        homeTypeOther: '', 
        timeAtAddress: '',

        // 3. Employment & Lifestyle
        occupation: '',
        petCaregiverWhenNotHome: '',

        // 4. Care & Commitment
        petSleepLocation: '',
        petFood: '',
        preparedForFinancialResponsibility: '',

        // 5. Emergency & Long-Term Planning
        emergencyPetCaregiver: '',

        // 6. References
        reference1Name: '',
        reference1Relationship: '',
        reference1Phone: '',
        reference1Email: '',
        reference2Name: '',
        reference2Relationship: '',
        reference2Phone: '',
        reference2Email: '',
        
        // 7. Acknowledgements & Consent (Step 2)
        certifyTrueInfo: false,
        authorizeVerification: false,
        signature: '', // Could be typed name as digital signature
        applicationDate: new Date().toISOString().slice(0, 10),
      },
      homePhotoFile: null,
      idPhotoFile: null,
      guardianIdPhotoFile: null,
    };
  },
  mounted() {
    this.checkScreenSize();
    window.addEventListener('resize', this.checkScreenSize);
    window.addEventListener('scroll', this.handleScroll);
    document.addEventListener('click', this.closeDropdownsOnClickOutside);
    this.addGlobalStyles();
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.checkScreenSize);
    window.removeEventListener('scroll', this.handleScroll);
    document.removeEventListener('click', this.closeDropdownsOnClickOutside);
    // Consider removing global styles if they were uniquely added by this component
    // and are no longer needed. For simplicity, this is often omitted unless critical.
  },
  methods: {
    // Global Styles
    addGlobalStyles() {
      const styleId = 'pawfect-adoption-global-styles';
      if (document.getElementById(styleId)) return;

      const style = document.createElement('style');
      style.id = styleId;
      style.innerHTML = `
        body {
          margin: 0;
          padding: 0;
        }
        html {
          box-sizing: border-box;
          overflow-x: hidden;
        }
        *, *:before, *:after {
          box-sizing: inherit;
        }
        body.no-scroll { /* Class to prevent scrolling when mobile menu or modal is open */
          overflow: hidden;
        }
        .guardian-section {
          margin-top: 15px;
          padding: 15px;
          background-color: #f9f9f9;
          border-left: 4px solid #3498db;
          border-radius: 4px;
        }
        .guardian-section h3 {
          margin-top: 0;
          color: #2980b9;
        }
      `;
      document.head.appendChild(style);
    },

    // Navigation Methods
    toggleMobileMenu() {
      this.mobileMenuOpen = !this.mobileMenuOpen;
      if (!this.mobileMenuOpen) {
        this.dropdownOpen = false; // Close any mobile submenu
      }
      document.body.classList.toggle('no-scroll', this.mobileMenuOpen);
    },
    toggleDropdown(event) { // Generic mobile dropdown toggle
      if (this.isMobile) {
        event.preventDefault();
        this.dropdownOpen = !this.dropdownOpen;
      }
    },
    toggleDesktopDropdown(event) {
      event.preventDefault();
      event.stopPropagation(); // Important to prevent closeDropdownsOnClickOutside from immediately firing
      if (this.isMobile) {
        // Fallback to generic mobile dropdown if needed, or a specific mobile action
        this.dropdownOpen = !this.dropdownOpen; 
        this.desktopDropdownOpen = false; // Ensure desktop one is closed on mobile
        this.userDropdownOpen = false;
      } else {
        this.desktopDropdownOpen = !this.desktopDropdownOpen;
        this.userDropdownOpen = false; // Close other dropdowns
      }
    },
    toggleUserDropdown(event) {
      event.preventDefault();
      event.stopPropagation(); // Important
      this.userDropdownOpen = !this.userDropdownOpen;
      this.desktopDropdownOpen = false; // Close other dropdowns
    },
    closeUserDropdown() {
      this.userDropdownOpen = false;
    },
    closeMenuIfMobile() {
      if (this.isMobile) {
        this.mobileMenuOpen = false;
        this.dropdownOpen = false;
        document.body.classList.remove('no-scroll');
      }
    },
    checkScreenSize() {
      this.isMobile = window.innerWidth <= 768;
      if (!this.isMobile) {
        // Close mobile-specific elements if screen becomes non-mobile
        this.mobileMenuOpen = false;
        this.dropdownOpen = false; 
        document.body.classList.remove('no-scroll');
      } else {
        // Close desktop-specific dropdowns if screen becomes mobile
        this.desktopDropdownOpen = false;
        this.userDropdownOpen = false; 
      }
    },
    handleScroll() {
      this.hasScrolled = window.scrollY > 20;
    },
    closeDropdownsOnClickOutside(event) {
      // Close desktop dropdown only
      // User dropdown will now only close via the X button (closeUserDropdown method)
      const desktopTrigger = this.$el.querySelector('.desktop-dropdown-trigger');
      const desktopMenu = this.$el.querySelector('.desktop-menu-content');
      if (this.desktopDropdownOpen && 
          !(desktopTrigger && desktopTrigger.contains(event.target)) &&
          !(desktopMenu && desktopMenu.contains(event.target))) {
        this.desktopDropdownOpen = false;
      }
      
      // We've removed the code that would close the user dropdown when clicking outside
    },

    // Phone number validation method
    validatePhoneInput(event, fieldName) {
      // Restrict input to numbers only
      const input = event.target.value;
      const numbersOnly = input.replace(/\D/g, '');
      
      // Update the field with numbers only
      this.application[fieldName] = numbersOnly;
    },

    // Application Form Methods
    openApplicationForm() {
      this.showApplicationForm = true;
      this.currentApplicationStep = 1;
      this.application.applicationDate = new Date().toISOString().slice(0,10);
      document.body.classList.add('no-scroll'); // Prevent body scroll when form is open
    },
    closeApplicationForm() {
      this.showApplicationForm = false;
      this.currentApplicationStep = 1;
      document.body.classList.remove('no-scroll');
    },
    proceedToUploadsAndFinalize() {
      const step1Form = this.$el.querySelector('.adoption-form'); // Ensure this selector targets the form for step 1
      if (step1Form && step1Form.checkValidity()) {
        // Additional validation for guardian info when user is under 18
        if (!this.application.ageOver18) {
          if (!this.application.guardianName || !this.application.guardianRelationship || 
              !this.application.guardianPhone || !this.application.guardianEmail || 
              !this.application.guardianConsent) {
            alert('Please complete all required guardian information fields.');
            return;
          }
        }
        
        this.currentApplicationStep = 2;
        const formContent = this.$el.querySelector('.form-content'); // Scrollable area within the form modal/popup
        if (formContent) formContent.scrollTop = 0;
      } else {
        if (step1Form) step1Form.reportValidity();
        alert('Please fill out all required fields in Step 1 before proceeding.');
      }
    },
    handleFileUpload(event, fileDataProperty) {
      const file = event.target.files[0];
      if (file) {
        this[fileDataProperty] = file;
        console.log(`${fileDataProperty} selected:`, file.name);
      } else {
        this[fileDataProperty] = null;
      }
    },
    submitFullApplication() {
      const step2Form = this.$el.querySelector('.adoption-form'); // Ensure this selector targets the form for step 2
      if (step2Form && step2Form.checkValidity()) {
        // Add explicit check for consent fields if not covered by form.checkValidity()
        if (!this.application.certifyTrueInfo || !this.application.authorizeVerification || !this.application.signature) {
            alert('Please complete all acknowledgements and provide your signature in Step 2.');
            return;
        }
        
        // Additional validation for guardian signature when user is under 18
        if (!this.application.ageOver18 && !this.application.guardianSignature) {
          alert("Please provide guardian's signature to complete the application.");
          return;
        }
        
        // Additional validation for guardian ID photo when user is under 18
        if (!this.application.ageOver18 && !this.guardianIdPhotoFile) {
          alert("Please upload guardian's valid ID to complete the application.");
          return;
        }
        
        // Make sure required files are uploaded
        if (!this.homePhotoFile) {
          alert("Please upload a photo of your home or pet area.");
          return;
        }
        
        if (!this.idPhotoFile) {
          alert("Please upload a copy of your valid ID.");
          return;
        }

        console.log('Full Application Submitted:', this.application);
        if (this.homePhotoFile) {
          console.log('Home Photo File:', this.homePhotoFile.name, this.homePhotoFile.type);
        }
        if (this.idPhotoFile) {
          console.log('ID Photo File:', this.idPhotoFile.name, this.idPhotoFile.type);
        }
        if (this.guardianIdPhotoFile) {
          console.log('Guardian ID Photo File:', this.guardianIdPhotoFile.name, this.guardianIdPhotoFile.type);
        }
        // TODO: Send data to a server (e.g., using FormData and fetch/axios)

        alert('Application submitted successfully!');
        this.closeApplicationForm(); // Also removes 'no-scroll' from body
      } else {
        if (step2Form) step2Form.reportValidity();
        alert('Please fill out all required fields in Step 2 and complete acknowledgements.');
      }
    }
  }
}
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
.nav-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #F9A826;
  padding: 0.75rem 2rem;
  color: white;
  position: sticky;
  top: 0;
  left: 0;
  right: 0;
  width: 100%;
  z-index: 1000;
  transition: all 0.3s ease;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.nav-scrolled {
  padding: 0.5rem 2rem;
  background-color: rgba(249, 168, 38, 0.95);
  backdrop-filter: blur(10px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.logo-container {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.logo-image {
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.3s ease;
}

.logo-container:hover .logo-image {
  transform: scale(1.1) rotate(5deg);
}

.logo-text {
  font-size: 1.75rem;
  font-weight: 800;
  letter-spacing: 1.5px;
  color: white;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.2);
  transition: transform 0.3s ease;
}

.logo-container:hover .logo-text {
  transform: translateX(3px);
}

.nav-links-container {
  flex: 1;
  display: flex;
  justify-content: center;
}

.nav-links {
  display: flex;
  gap: 2rem;
  align-items: center;
}

.nav-link {
  color: white;
  text-decoration: none;
  font-size: 1.1rem;
  font-weight: 600;
  position: relative;
  padding: 0.5rem 0;
  transition: all 0.2s ease;
}

.nav-link:after {
  content: '';
  position: absolute;
  width: 0;
  height: 2px;
  bottom: 0;
  left: 0;
  background-color: white;
  transition: width 0.3s ease;
}

.nav-link:hover:after {
  width: 100%;
}

.dropdown {
  position: relative;
}

.dropdown-arrow {
  font-size: 0.7rem;
  vertical-align: middle;
  margin-left: 4px;
  transition: transform 0.3s ease;
}

.arrow-rotated {
  transform: rotate(180deg);
}

.dropdown-content {
  min-width: 180px;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  overflow: hidden;
  z-index: 1;
}

.dropdown-content.desktop {
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  margin-top: 0.75rem;
  background-color: white;
  z-index: 1001;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.15);
  border-radius: 8px;
}

.dropdown-content.desktop-active {
  animation: dropdownIn 0.3s ease-out forwards;
}

.dropdown-content a {
  padding: 0.75rem 1rem;
  text-decoration: none;
  display: block;
  transition: all 0.2s ease;
}

.dropdown-content.desktop a {
  color: #333;
  font-weight: 500;
}

.dropdown-content.desktop a:hover {
  background-color: #f8f8f8;
  padding-left: 1.25rem;
}

.dropdown-content.mobile {
  background-color: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  margin-top: 0.5rem;
}

.dropdown-content.mobile a {
  color: white;
  padding: 0.75rem 1.5rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.dropdown-content.mobile a:last-child {
  border-bottom: none;
}

.right-section {
  display: flex;
  align-items: center;
  gap: 1.5rem;
}

.user-dropdown {
  position: relative;
}

.user-icon {
  color: white;
  cursor: pointer;
  width: 36px;
  height: 36px;
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.user-icon:hover {
  transform: scale(1.1);
  background-color: rgba(255, 255, 255, 0.3);
}

.user-dropdown-content {
  position: absolute;
  top: calc(100% + 0.5rem);
  right: 0;
  background-color: white;
  min-width: 220px;
  max-width: 90vw;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
  border-radius: 12px;
  overflow: hidden;
  z-index: 1001;
  border: 1px solid rgba(0, 0, 0, 0.08);
}

.dropdown-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.75rem 1rem;
  background-color: #f8f8f8;
  border-bottom: 1px solid rgba(0, 0, 0, 0.08);
}

.dropdown-header span {
  font-weight: 600;
  color: #333;
}

.close-dropdown-btn {
  background: transparent;
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  border-radius: 50%;
  color: #666;
  transition: all 0.2s ease;
}

.close-dropdown-btn:hover {
  background-color: rgba(0, 0, 0, 0.05);
  color: #333;
}

.user-dropdown-content a {
  color: #333;
  padding: 0.9rem 1.2rem;
  text-decoration: none;
  display: block;
  font-weight: 500;
  transition: all 0.2s ease;
  border-bottom: 1px solid rgba(0, 0, 0, 0.05);
}

.user-dropdown-content a:last-child {
  border-bottom: none;
}

.user-dropdown-content a:hover {
  background-color: #f8f8f8;
  padding-left: 1.5rem;
}

.user-dropdown-content.mobile-dropdown {
  position: absolute;
  width: auto;
  min-width: 220px;
  max-width: 90vw;
}

.dropdown-animation-enter-active {
  animation: dropdownIn 0.3s ease-out forwards;
}

.dropdown-animation-leave-active {
  animation: dropdownOut 0.3s ease-in forwards;
}

@keyframes dropdownIn {
  0% {
    opacity: 0;
    transform: translateY(-10px);
  }
  60% {
    transform: translateY(5px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes dropdownOut {
  0% {
    opacity: 1;
    transform: translateY(0);
  }
  100% {
    opacity: 0;
    transform: translateY(-10px);
  }
}

.resources-dropdown-enter-active {
  animation: dropdownResourcesIn 0.3s ease-out forwards;
}

.resources-dropdown-leave-active {
  animation: dropdownResourcesOut 0.3s ease-in forwards;
}

@keyframes dropdownResourcesIn {
  0% {
    opacity: 0;
    transform: translateY(-10px) translateX(-50%);
  }
  60% {
    transform: translateY(5px) translateX(-50%);
  }
  100% {
    opacity: 1;
    transform: translateY(0) translateX(-50%);
  }
}

@keyframes dropdownResourcesOut {
  0% {
    opacity: 1;
    transform: translateY(0) translateX(-50%);
  }
  100% {
    opacity: 0;
    transform: translateY(-10px) translateX(-50%);
  }
}

.mobile-menu-toggle {
  display: none;
  flex-direction: column;
  justify-content: space-between;
  width: 28px;
  height: 20px;
  background: transparent;
  border: none;
  cursor: pointer;
  padding: 0;
  z-index: 10;
}

.bar {
  height: 3px;
  width: 100%;
  background-color: white;
  border-radius: 10px;
  transition: all 0.3s ease-in-out;
}

.bar-1-active {
  transform: translateY(8px) rotate(45deg);
}

.bar-2-active {
  opacity: 0;
}

.bar-3-active {
  transform: translateY(-8px) rotate(-45deg);
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from, .fade-leave-to {
  opacity: 0;
}

.slide-fade-enter-active, .slide-fade-leave-active {
  transition: all 0.3s ease;
}

.slide-fade-enter-from, .slide-fade-leave-to {
  transform: translateY(-20px);
  opacity: 0;
}

@media (max-width: 900px) {
  .nav-links {
    gap: 1.5rem;
  }
}

@media (max-width: 768px) {
  .nav-container {
    padding: 0.75rem 1.25rem;
  }
  
  .logo-text {
    font-size: 1.5rem;
  }
  
  .mobile-menu-toggle {
    display: flex;
  }
  
  .nav-links-container {
    position: static;
  }
  
  .nav-links {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    background-color: #F9A826;
    padding: 2rem;
    gap: 1.5rem;
    z-index: 5;
    transition: all 0.3s ease;
    overflow-y: auto;
    padding-top: 5rem;
    width: 100vw;
    margin: 0;
  }
  
  .mobile-active {
    animation: slideIn 0.3s forwards;
  }
  
  .nav-link {
    font-size: 1.3rem;
  }
  
  .nav-link:after {
    height: 3px;
  }
}

@keyframes slideIn {
  0% {
    transform: translateX(100%);
  }
  100% {
    transform: translateX(0);
  }
}

@media (max-width: 480px) {
  .nav-container {
    padding: 0.75rem 1rem;
  }
  
  .logo-text {
    font-size: 1.3rem;
  }
  
  .user-icon {
    width: 32px;
    height: 32px;
  }
  
  .nav-links {
    padding: 1.5rem;
    gap: 1.25rem;
  }
}

@media (max-width: 320px) {
  .nav-container {
    padding: 0.75rem 0.5rem;
  }
  
  .logo-text {
    font-size: 1.1rem;
  }
  
  .right-section {
    gap: 0.75rem;
  }
  
  .user-dropdown-content {
    min-width: 200px;
  }
}

@media (max-width: 245px) {
  .logo-text {
    font-size: 0.9rem;
  }
  
  .user-icon {
    width: 28px;
    height: 28px;
  }
  
  .nav-container {
    padding: 0.5rem 0.25rem;
  }
  
  .user-dropdown-content {
    min-width: 180px;
    right: -20px; 
  }
}


/* Pet Profile Card Styles */
.pet-profile-container {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 2rem 1rem; /* Reduced horizontal padding for small screens */
  background-color: #f8f9fa; 
}

.pet-card {
  display: flex;
  flex-direction: row; /* Default to row layout */
  max-width: 900px; 
  width: 100%;
  background-color: white;
  border-radius: 12px;
  overflow: hidden; 
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.08);
  transition: transform 0.3s ease;
}

.pet-card:hover {
  transform: translateY(-5px);
}

.pet-image-container {
  width: 100%;
  height: 300px;
  min-width: 200px;
  max-width: 400px;
  position: relative;
  overflow: hidden;
  background: #eee;
  border-radius: 12px 0 0 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.pet-image {
  max-width: 100%;
  max-height: 100%;
  width: auto;
  height: auto;
  object-fit: contain;
  display: block;
  margin: auto;
}

.pet-info {
  flex: 1.5; 
  padding: 2rem;
  display: flex;
  flex-direction: column;
}

.pet-name {
  color: #546e7a; 
  font-size: 2.2rem;
  margin-top: 0;
  margin-bottom: 1.5rem;
  font-weight: 700;
}

.pet-details {
  display: flex;
  flex-wrap: wrap; 
  gap: 1rem; 
  margin-bottom: 1.5rem;
}

.detail-item {
  background-color: #f1f8fe; 
  padding: 0.5rem 1rem;
  border-radius: 20px; 
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.detail-label {
  color: #607d8b; 
  font-weight: 600;
}

.detail-value {
  color: #455a64; 
}

.pet-description {
  color: #607d8b;
  line-height: 1.6;
  font-size: 1.1rem;
  margin-bottom: 2rem;
  flex-grow: 1; 
}

.adoption-button {
  align-self: flex-start; 
  background-color: #F9A826;
  color: white;
  border: none;
  padding: 0.8rem 1.5rem;
  border-radius: 30px;
  font-weight: 600;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 8px rgba(249, 168, 38, 0.3);
}

.adoption-button:hover {
  background-color: #e89921; 
  transform: translateY(-2px);
}

/* Process adoption */
.adoption-process {
  background-color: #f8f9fa;
  padding: 3rem 1rem;
  margin-bottom: 2rem;
}

.container {
  text-align: center;
  margin: 0 auto;
  max-width: 1200px;
}

.title {
  font-size: 2rem;
  font-weight: bold;
  margin-bottom: 2rem;
  color: #546e7a;
}

.steps {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  gap: 10px;
}

.step {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  padding: 1rem;
  width: 120px; 
}

.circle {
  background-color: #F9A826;
  color: white;
  font-weight: bold;
  font-size: 1.25rem;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 0.75rem; 
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.step:hover .circle {
  transform: scale(1.1);
  box-shadow: 0 6px 12px rgba(0,0,0,0.15);
}

.line {
  width: 50px; 
  height: 3px;
  background-color: #F9A826;
  position: relative; 
}

.step p {
  color: #607d8b;
  font-weight: 600;
  margin-top: 0.5rem;
}

/* Application Form Styles */
.application-form-section {
  width: 100%;
}

.form-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.6); /* Darker overlay */
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2000;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.3s ease, visibility 0.3s ease;
}

.show-form {
  opacity: 1;
  visibility: visible;
}

.form-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}

.form-content {
  background: white;
  border-radius: 10px;
  width: 90%;
  max-width: 800px; 
  max-height: 90vh; 
  overflow-y: auto; 
  padding: 25px 35px; 
  position: relative;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
  transform: translateY(20px);
  transition: transform 0.3s ease;
}

.show-form .form-content {
  transform: translateY(0);
}

.close-form-btn {
  position: absolute;
  top: 15px;
  right: 15px;
  background: none;
  border: none;
  font-size: 28px;
  cursor: pointer;
  color: #aaa; 
  transition: color 0.3s ease;
}

.close-form-btn:hover {
  color: #F9A826;
}

.form-title {
  font-size: 26px; 
  color: #F9A826; 
  margin-bottom: 8px;
  text-align: center;
}

.form-description {
  color: #777; 
  text-align: center;
  margin-bottom: 25px;
  line-height: 1.5;
}

.adoption-form h3 {
    font-size: 1.5rem;
    color: #546e7a;
    margin-top: 25px;
    margin-bottom: 15px;
    padding-bottom: 5px;
    border-bottom: 1px solid #eee;
}
.adoption-form h3:first-of-type {
    margin-top: 0;
}


.form-group {
  margin-bottom: 18px; 
}

.input-row {
  display: flex;
  flex-wrap: wrap; /* Allow wrapping on smaller screens */
  gap: 18px;
  margin-bottom: 18px;
}

.input-row .form-group {
  flex: 1;
  min-width: 250px; /* Minimum width before wrapping */
  margin-bottom: 0; 
}

.form-group label {
  display: block;
  margin-bottom: 8px; 
  font-weight: 600;
  color: #555;
  font-size: 0.95rem;
}
.form-group label.checkbox-label {
    font-weight: normal;
    display: flex;
    align-items: center;
    gap: 8px;
    color: #666;
}
.form-group label.checkbox-label input[type="checkbox"] {
    width: auto;
    margin-right: 5px;
    accent-color: #F9A826;

}
.form-group input[type="text"],
.form-group input[type="email"],
.form-group input[type="tel"],
.form-group input[type="date"],
.form-group textarea,
.form-group select {
  width: 100%;
  padding: 12px 15px; 
  border: 1px solid #ccc; 
  border-radius: 6px; 
  font-size: 1rem;
  transition: border-color 0.3s ease, box-shadow 0.3s ease;
  background-color: #fdfdfd;
}
.form-group input[type="radio"] {
    margin-right: 5px;
    accent-color: #F9A826;
}
.form-group div > label { 
    margin-right: 15px;
    font-weight: normal;
    color: #666;
}


.form-group textarea {
    min-height: 80px;
    resize: vertical;
}
.form-group input:focus,
.form-group textarea:focus,
.form-group select:focus {
  border-color: #F9A826;
  box-shadow: 0 0 0 3px rgba(249, 168, 38, 0.25); 
  outline: none;
}
.mt-small {
    margin-top: 10px;
}

.form-actions {
  margin-top: 30px; 
  text-align: right; 
  display: flex;
  justify-content: flex-end;
  gap: 10px;
  flex-wrap: wrap; /* Allow buttons to wrap on very small screens */
}

.submit-btn {
  background-color: #F9A826;
  color: white;
  border: none;
  padding: 12px 25px;
  border-radius: 6px;
  font-size: 1rem; 
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.2s ease;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.submit-btn:hover {
  background-color: #e89921;
  transform: translateY(-1px);
}
.submit-btn.secondary-btn {
    background-color: #777;
}
.submit-btn.secondary-btn:hover {
    background-color: #666;
}

/* New responsive styles for 601px to 1000px */
@media (min-width: 600px) and (max-width: 1000px) {
  .pet-card {
    flex-direction: column; /* Stack elements vertically in this range */
  }
  
  .pet-image-container {
    min-width: 100%;
    max-width: 100%;
    height: 300px; /* Slightly taller than mobile */
  }
  
  .pet-info {
    padding: 1.75rem;
  }
  
  .pet-name {
    font-size: 2rem;
    margin-bottom: 1.25rem;
  }
  
  .adoption-button {
    align-self: center;
    padding: 0.8rem 2rem;
    width: 80%;
    text-align: center;
  }
  
  .input-row {
    flex-direction: column;
  }
  
  .input-row .form-group {
    margin-bottom: 15px;
  }
  
  .steps {
    justify-content: space-around;
    flex-wrap: wrap;
  }
  
  .line {
    width: 30px; /* Shorter connecting lines */
  }
  
  .form-content {
    width: 85%;
    padding: 22px 30px;
  }
}

/* New responsive styles for pet card */
@media (max-width: 768px) {
  .pet-profile-container {
    padding: 1.5rem 1rem;
  }
  
  .pet-card {
    flex-direction: column; /* Stack image and content vertically */
  }
  
  .pet-image-container {
    min-width: 100%;
    max-width: 100%;
    height: 250px; /* Fixed height for the image on mobile */
  }
  
  .pet-info {
    padding: 1.5rem;
  }
  
  .pet-name {
    font-size: 1.8rem;
    margin-bottom: 1rem;
  }
  
  .pet-details {
    margin-bottom: 1rem;
  }
  
  .form-content {
    padding: 20px 25px;
  }
}

@media (max-width: 600px) {
  .steps {
    flex-direction: column; 
  }
  
  .line {
    width: 3px; 
    height: 30px;
  }
  
  .pet-profile-container {
    padding: 1rem 0.75rem;
  }
  
  .pet-name {
    font-size: 1.6rem;
  }
  
  .pet-description {
    font-size: 1rem;
  }
  
  .adoption-button {
    width: 100%; /* Full width button on small screens */
    padding: 0.75rem 1rem;
    text-align: center;
  }
  
  .form-title {
    font-size: 22px;
  }
  
  .form-actions {
    flex-direction: column;
    width: 100%;
  }
  
  .submit-btn {
    width: 100%;
  }
}

@media (max-width: 480px) {
  .pet-profile-container {
    padding: 1rem 0.5rem;
  }
  
  .pet-info {
    padding: 1.25rem 1rem;
  }
  
  .pet-name {
    font-size: 1.5rem;
  }
  
  .detail-item {
    padding: 0.4rem 0.8rem;
    font-size: 0.9rem;
  }
  
  .form-content {
    padding: 15px 20px;
    width: 95%;
  }
  
  .title {
    font-size: 1.5rem;
    margin-bottom: 1.5rem;
  }
}

@media (max-width: 320px) {
  .pet-name {
    font-size: 1.3rem;
  }
  
  .pet-details {
    gap: 0.5rem;
  }
  
  .detail-item {
    font-size: 0.8rem;
    padding: 0.3rem 0.6rem;
  }
  
  .pet-description {
    font-size: 0.9rem;
    line-height: 1.5;
  }
  
  .form-group label {
    font-size: 0.85rem;
  }
  
  .form-group input[type="text"],
  .form-group input[type="email"],
  .form-group input[type="tel"],
  .form-group input[type="date"],
  .form-group textarea,
  .form-group select {
    font-size: 0.9rem;
    padding: 10px 12px;
  }
}
</style>