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
            <a href="home" class="nav-link" @click="closeMenuIfMobile">Home</a>
            <a href="pet-profiles" class="nav-link" @click="closeMenuIfMobile">Pet Profiles</a>
            <div class="dropdown">
              <a href="#" class="nav-link dropdown-toggle" @click="toggleDesktopDropdown">
                Resources <span class="dropdown-arrow" :class="{ 'arrow-rotated': (isMobile ? dropdownOpen : desktopDropdownOpen) }">▼</span>
              </a>
              <transition name="slide-fade">
                <div v-if="isMobile && dropdownOpen" class="dropdown-content mobile">
                  <a href="training" @click="closeMenuIfMobile">Training Tips</a>
                  <a href="stories" @click="closeMenuIfMobile">Success Stories</a>
                </div>
              </transition>
              <transition name="resources-dropdown">
                <div v-if="!isMobile && desktopDropdownOpen" class="dropdown-content desktop">
                  <a href="training">Training Tips</a>
                  <a href="stories">Success Stories</a>
                </div>
              </transition>
            </div>
            <a href="donations" class="nav-link" @click="closeMenuIfMobile">Donation</a>
            <a href="stories" class="nav-link" @click="closeMenuIfMobile">About</a>
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
              <a href="#" @click.stop>Profile</a>
              <a href="status" @click.stop>Status</a>
              <a href="#" @click.stop>Log Out</a>
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
    <div class="pet-profiles-container">
      <h1>Pet Profiles</h1>
      <div class="pet-grid">
        <div v-for="pet in pets" :key="pet.id" class="pet-card">
          <!-- <img :src="pet.image" :alt="pet.name" class="pet-img" /> -->
          <h2>{{ pet.name }}</h2>
          <p class="pet-breed">{{ pet.breed }} &bull; {{ pet.age }}</p>
          <p class="pet-desc">{{ pet.background }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'PetProfiles',
  data() {
    return {
      mobileMenuOpen: false,
      dropdownOpen: false, // For mobile resources dropdown
      desktopDropdownOpen: false, // For desktop resources dropdown
      userDropdownOpen: false,
      isMobile: false,
      hasScrolled: false,
      pets: [],
      loading: false,
      error: null
    };
  },
  async created() {
    await this.fetchPets();
  },
  mounted() {
    this.checkScreenSize();
    window.addEventListener('resize', this.checkScreenSize);
    window.addEventListener('scroll', this.handleScroll);
    document.addEventListener('click', this.closeResourceDropdownOnClickOutside);
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.checkScreenSize);
    window.removeEventListener('scroll', this.handleScroll);
    document.removeEventListener('click', this.closeResourceDropdownOnClickOutside);
  },
  methods: {
    async fetchPets() {
      this.loading = true;
      this.error = null;
      try {
        const response = await axios.get('http://localhost:5000/api/pets');
        this.pets = response.data;
      } catch (err) {
        this.error = 'Failed to load pets. Please try again later.';
        console.error('Error fetching pets:', err);
      } finally {
        this.loading = false;
      }
    },
    toggleMobileMenu() {
      this.mobileMenuOpen = !this.mobileMenuOpen;
      if (!this.mobileMenuOpen) {
        this.dropdownOpen = false; // Close mobile resources dropdown if menu closes
      }
      if (this.mobileMenuOpen) {
        document.body.classList.add('no-scroll');
      } else {
        document.body.classList.remove('no-scroll');
      }
    },
    toggleDesktopDropdown(event) {
      event.preventDefault();
      event.stopPropagation();
      if (this.isMobile) {
        // Toggle mobile resources dropdown
        this.dropdownOpen = !this.dropdownOpen;
        // Ensure other main dropdowns are managed if needed, but userDropdownOpen is independent now
        this.desktopDropdownOpen = false; 
      } else {
        // Toggle desktop resources dropdown
        this.desktopDropdownOpen = !this.desktopDropdownOpen;
      }
      // NOTE: userDropdownOpen is NOT affected by this action anymore.
    },
    toggleUserDropdown(event) {
      event.preventDefault();
      event.stopPropagation();
      // If the user menu is not already open, open it.
      // If it is already open, clicking the icon again does nothing to its state,
      // as it can now only be closed by its 'X' button.
      if (!this.userDropdownOpen) {
        this.userDropdownOpen = true;
      }
      // When user dropdown is interacted with, ensure other (non-user) dropdowns are closed.
      this.desktopDropdownOpen = false;
      if (this.isMobile) {
        this.dropdownOpen = false; // mobile resources dropdown
      }
    },
    closeUserDropdown() {
      // This is the only method that should set userDropdownOpen to false.
      this.userDropdownOpen = false;
    },
    closeMenuIfMobile() {
      if (this.isMobile) {
        this.mobileMenuOpen = false;
        this.dropdownOpen = false; // Close mobile resources dropdown
        document.body.classList.remove('no-scroll');
      }
      // userDropdownOpen is not affected here.
      // desktopDropdownOpen is not affected here by simple link clicks.
    },
    checkScreenSize() {
      const previouslyMobile = this.isMobile;
      this.isMobile = window.innerWidth <= 768;

      if (previouslyMobile !== this.isMobile) { // Only run logic if mode actually changes
        if (!this.isMobile) {
          // Switched to Desktop
          this.mobileMenuOpen = false;
          this.dropdownOpen = false; // mobile resources dropdown
          document.body.classList.remove('no-scroll');
        } else {
          // Switched to Mobile
          this.desktopDropdownOpen = false;
        }
      }
      // userDropdownOpen is NOT affected by screen size changes.
    },
    handleScroll() {
      this.hasScrolled = window.scrollY > 20;
    },
    closeResourceDropdownOnClickOutside(event) {
      // This function now ONLY handles closing the desktop resources dropdown.
      // User dropdown is not affected by clicks outside.
      const resourcesDropdownToggle = this.$el.querySelector('.nav-link.dropdown-toggle'); // Assuming this is specific enough
      const resourcesDropdownContent = this.$el.querySelector('.dropdown-content.desktop');

      if (this.desktopDropdownOpen && resourcesDropdownToggle && !resourcesDropdownToggle.contains(event.target) && resourcesDropdownContent && !resourcesDropdownContent.contains(event.target)) {
        this.desktopDropdownOpen = false;
      }
    },
  },
};
</script>

<style scoped>
/* Added for mobile menu open state */
body.no-scroll {
  overflow: hidden;
}

/* Navigation bar styles */
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
  cursor: pointer; /* Added for better UX */
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
  z-index: 1001; /* Ensure it's above nav-scrolled backdrop */
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
  cursor: pointer; /* Added for better UX */
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
  z-index: 1001; /* Ensure it's above nav-scrolled backdrop */
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

/* Animations for dropdowns */
.dropdown-animation-enter-active {
  animation: dropdownIn 0.3s ease-out forwards;
}
.dropdown-animation-leave-active {
  animation: dropdownOut 0.3s ease-in forwards;
}
@keyframes dropdownIn {
  0% { opacity: 0; transform: translateY(-10px); }
  60% { transform: translateY(5px); }
  100% { opacity: 1; transform: translateY(0); }
}
@keyframes dropdownOut {
  0% { opacity: 1; transform: translateY(0); }
  100% { opacity: 0; transform: translateY(-10px); }
}
.resources-dropdown-enter-active {
  animation: dropdownResourcesIn 0.3s ease-out forwards;
}
.resources-dropdown-leave-active {
  animation: dropdownResourcesOut 0.3s ease-in forwards;
}
@keyframes dropdownResourcesIn {
  0% { opacity: 0; transform: translateY(-10px) translateX(-50%); }
  60% { transform: translateY(5px) translateX(-50%); }
  100% { opacity: 1; transform: translateY(0) translateX(-50%); }
}
@keyframes dropdownResourcesOut {
  0% { opacity: 1; transform: translateY(0) translateX(-50%); }
  100% { opacity: 0; transform: translateY(-10px) translateX(-50%); }
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
  z-index: 1005; /* Ensure it's above nav-links when they are fixed */
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
    z-index: 1000;
    transition: transform 0.3s ease;
    transform: translateX(100%);
    overflow-y: auto;
    padding-top: 5rem;
    width: 100vw;
    margin: 0;
  }
  .nav-links.mobile-active {
    transform: translateX(0);
  }
  .nav-link {
    font-size: 1.3rem;
  }
  .nav-link:after {
    height: 3px;
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
  .nav-links.mobile-active {
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

/* Pet profiles styles (as before) */
.pet-profiles-container {
  max-width: 1100px;
  margin: 2rem auto;
  padding: 2rem;
  background: #fff;
  border-radius: 16px;
  box-shadow: 0 4px 24px rgba(0,0,0,0.08);
}
.pet-profiles-container h1 {
  text-align: center;
  color: #f7871f;
  margin-bottom: 2rem;
}
.pet-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 2rem;
}
.pet-card {
  background: #f7f7f7;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.04);
  padding: 1.5rem 1rem 1rem 1rem;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.pet-img {
  width: 120px;
  height: 120px;
  object-fit: cover;
  border-radius: 50%;
  margin-bottom: 1rem;
  border: 4px solid #fff6e9;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
}
.pet-card h2 {
  margin: 0.5rem 0 0.2rem 0;
  color: #f7871f;
  font-size: 1.3rem;
}
.pet-breed {
  color: #888;
  font-size: 1rem;
  margin-bottom: 0.7rem;
}
.pet-desc {
  color: #444;
  font-size: 0.98rem;
  margin-bottom: 0.5rem;
}
.loading {
  text-align: center;
  padding: 2rem;
  color: #f7871f;
  font-size: 1.2rem;
}

.error {
  text-align: center;
  padding: 2rem;
  color: #ff5252;
  font-size: 1.2rem;
}
</style>