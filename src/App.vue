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

<script lang="ts">
import { ref, reactive, onMounted } from 'vue';
import { toPng } from 'dom-to-image-more';

interface AvatarPart {
  type: string;
  images: Record<string, any>;
  top: string;
  left: string;
  transform: string;
}

interface GeneratedPart {
  src: string;
  top: string;
  left: string;
  transform: string;
}

const parts: AvatarPart[] = [
  {
    type: 'background',
    images: import.meta.glob('./assets/avatar-parts/background/*.png', {
      eager: true,
    }),
    top: '0',
    left: '0',
    transform: '0',
  },
  {
    type: 'head',
    images: import.meta.glob('./assets/avatar-parts/head/*.png', {
      eager: true,
    }),
    top: '125px',
    left: '50%',
    transform: 'translateX(-50%)',
  },
  {
    type: 'eyes',
    images: import.meta.glob('./assets/avatar-parts/eyes/*.png', {
      eager: true,
    }),
    top: '196px',
    left: '50%',
    transform: 'translateX(-50%)',
  },
  {
    type: 'eyebrows',
    images: import.meta.glob('./assets/avatar-parts/eyebrows/*.png', {
      eager: true,
    }),
    top: '177px',
    left: '50%',
    transform: 'translateX(-50%)',
  },
  {
    type: 'mouth',
    images: import.meta.glob('./assets/avatar-parts/mouth/*.png', {
      eager: true,
    }),
    top: '172px',
    left: '50%',
    transform: 'translateX(-50%)',
  },
  {
    type: 'glasses',
    images: import.meta.glob('./assets/avatar-parts/glasses/*.png', {
      eager: true,
    }),
    top: '191px',
    left: '50%',
    transform: 'translateX(-50%)',
  },
  {
    type: 'body',
    images: import.meta.glob('./assets/avatar-parts/body/*.png', {
      eager: true,
    }),
    top: '290px',
    left: '50%',
    transform: 'translateX(-50%)',
  },
  {
    type: 'top',
    images: import.meta.glob('./assets/avatar-parts/top/*.png', {
      eager: true,
    }),
    top: '58px',
    left: '50%',
    transform: 'translateX(-50%)',
  },
  {
    type: 'pet',
    images: import.meta.glob('./assets/avatar-parts/pet/*.png', {
      eager: true,
    }),
    top: '260px',
    left: '-80px',
    transform: 'rotate(-20deg)',
  },
];

const getRandomImage = (images: Record<string, any>): string => {
  const keys = Object.keys(images);
  const randomIndex = Math.floor(Math.random() * keys.length);
  const imagePath = keys[randomIndex];
  const image = images[imagePath];
  return image && image.default ? image.default : '';
};

const downloadFile = async (url: string, fileName: string): Promise<void> => {
  try {
    const response = await fetch(url);
    const blob = await response.blob();
    const objectURL = URL.createObjectURL(blob);

    const link = document.createElement('a');
    link.href = objectURL;
    link.download = fileName;
    link.click();

    setTimeout(() => {
      URL.revokeObjectURL(objectURL);
      link.remove();
    }, 100);
  } catch (error) {
    console.error('Download error:', error);
  }
};

export default {
  setup() {
    const avatar = ref<HTMLElement | null>(null);
    const avatarParts = reactive<GeneratedPart[]>([]);

    const generateAvatar = (): void => {
      avatarParts.splice(0, avatarParts.length);

      parts.forEach((part: AvatarPart) => {
        avatarParts.push({
          src: getRandomImage(part.images),
          top: part.top,
          left: part.left,
          transform: part.transform,
        });
      });
    };

    const downloadAvatar = async (): Promise<void> => {
      if (!avatar.value) return;
      const dataUrl = await toPng(avatar.value);
      const fileName = 'avatar.png';
      downloadFile(dataUrl, fileName);
    };

    onMounted(() => {
      generateAvatar();
    });

    return { avatar, avatarParts, generateAvatar, downloadAvatar };
  },
};
</script>

<style src="./App.css" scoped></style>
