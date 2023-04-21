<template>
  <div class="avatar" ref="avatar">
    <img
      class="avatar-image"
      v-for="(part, index) in avatarParts"
      :key="index"
      :src="part.src"
      :style="{
        position: 'absolute',
        top: part.top,
        left: part.left,
        transform: part.transform,
      }"
    />
  </div>
  <button class="button" @click="generateAvatar">Generate</button>
  <button class="button" @click="downloadAvatar">Download</button>
</template>

<script>
import { ref, reactive, onMounted } from 'vue';
import { toPng } from 'dom-to-image-more';
import { saveAs } from 'file-saver';

export default {
  setup() {
    const avatar = ref(null);
    const avatarParts = reactive([]);

    const parts = [
      {
        type: 'background',
        images: import.meta.globEager('./assets/avatar-parts/background/*.png'),
        top: '0',
        left: '0',
      },
      {
        type: 'head',
        images: import.meta.globEager('./assets/avatar-parts/head/*.png'),
        top: '125px',
        left: '50%',
        transform: 'translateX(-50%)',
      },
      {
        type: 'eyes',
        images: import.meta.globEager('./assets/avatar-parts/eyes/*.png'),
        top: '196px',
        left: '50%',
        transform: 'translateX(-50%)',
      },
      {
        type: 'eyebrows',
        images: import.meta.globEager('./assets/avatar-parts/eyebrows/*.png'),
        top: '177px',
        left: '50%',
        transform: 'translateX(-50%)',
      },
      {
        type: 'mouth',
        images: import.meta.globEager('./assets/avatar-parts/mouth/*.png'),
        top: '172px',
        left: '50%',
        transform: 'translateX(-50%)',
      },
      {
        type: 'glasses',
        images: import.meta.globEager('./assets/avatar-parts/glasses/*.png'),
        top: '191px',
        left: '50%',
        transform: 'translateX(-50%)',
      },
      {
        type: 'body',
        images: import.meta.globEager('./assets/avatar-parts/body/*.png'),
        top: '290px',
        left: '50%',
        transform: 'translateX(-50%)',
      },
      {
        type: 'top',
        images: import.meta.globEager('./assets/avatar-parts/top/*.png'),
        top: '58px',
        left: '50%',
        transform: 'translateX(-50%)',
      },
      {
        type: 'pet',
        images: import.meta.globEager('./assets/avatar-parts/pet/*.png'),
        top: '260px',
        left: '-80px',
        transform: 'rotate(-20deg)',
      },
    ];

    const getRandomImage = (images) => {
      const keys = Object.keys(images);
      const randomIndex = Math.floor(Math.random() * keys.length);
      const imagePath = keys[randomIndex];
      const image = images[imagePath];
      return image && image.default ? image.default : '';
    };

    const generateAvatar = () => {
      avatarParts.splice(0, avatarParts.length);

      parts.forEach((part) => {
        avatarParts.push({
          src: getRandomImage(part.images),
          top: part.top,
          left: part.left,
          transform: part.transform,
        });
      });
    };

    const downloadAvatar = async () => {
      const dataUrl = await toPng(avatar.value);
      saveAs(dataUrl, 'avatar.png');
    };

    onMounted(() => {
      generateAvatar();
    });

    return { avatar, avatarParts, generateAvatar, downloadAvatar };
  },
};
</script>

<style src="./App.css" scoped></style>
