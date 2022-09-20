<template>
  <div class="profile-page">
    <div v-if="isOwnProfile" class="button-top">
      <router-link class="button" to="/profile">Edit Profile</router-link>
      <button @click="handleLogout">Logout</button>
    </div>
    <div class="profile-wrapper">
      <div class="profile-header">
        <img
          class="image-avatar"
          width="90"
          height="90"
          :src="image || 'https://via.placeholder.com/90'"
          alt=""
        />
        <h3 class="profile-title">
          <span>{{ firstName }}</span> <span>{{ lastName }}</span>
        </h3>
        <p>{{ position || 'position is null' }}</p>
      </div>
      <div class="card">
        <h3>Contact</h3>
        <div class="card-field">
          <span>Name</span>
          <p>{{ firstName }} {{ lastName }}</p>
        </div>
        <div class="card-field">
          <span>Mobile</span>
          <p>{{ phoneNumber || 'phone number is null' }}</p>
        </div>
        <div class="card-field">
          <span>Email</span>
          <a class="link-email" :href="'mailto:' + email">
            {{ email || 'email is null' }}
          </a>
        </div>
        <div class="card-field">
          <span>Company</span>
          <p>{{ company || 'company is null' }}</p>
        </div>
      </div>
      <div class="card">
        <h3>Location</h3>
        <p>{{ location || 'location is null' }}</p>
      </div>
      <div class="card">
        <h3>Web Links</h3>
        <a class="website-link" href="{{ website || '' }}"> Website </a>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import { microgen } from '../lib/microgen';

const router = useRouter();
const route = useRoute();
const firstName = ref('');
const lastName = ref('');
const phoneNumber = ref('');
const email = ref('');
const profileId = ref('');
const image = ref('');
const company = ref('');
const position = ref('');
const location = ref('');
const website = ref('');
const isOwnProfile = ref(false);

const getProfile = async () => {
  const { username } = route.params;

  if (!username) {
    return;
  }

  const { data, error } = await microgen.service('Users').find({
    where: {
      username,
    },
    lookup: '*',
  });

  if (error) {
    console.log(error);
    return;
  }

  if (data) {
    const user = data?.[0];
    const profile = user?.profile?.[0];
    firstName.value = user.firstName;
    lastName.value = user.lastName;
    phoneNumber.value = user.phoneNumber;
    email.value = user.email;
    profileId.value = profile._id;
    image.value = profile.image;
    company.value = profile.company;
    location.value = profile.location;
    position.value = profile.position;
    website.value = profile.website;

    const { user: authUser } = await microgen.auth.user();

    isOwnProfile.value = user.username === authUser?.username;
  }
};

const handleLogout = async () => {
  const { error } = await microgen.auth.logout();

  if (error) {
    console.log(error);
    return;
  }

  router.push('/');
};

onMounted(() => {
  getProfile();
});
</script>
