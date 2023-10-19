<!-- <template>
  <div>
    <input type="file" @change="previewImage">
    <img v-if="imageSrc" :src="imageSrc" alt="Preview" ref="image">
    <button @click="convertToPDF">Convert to PDF</button>
  </div>
</template>

<script>
import { jsPDF } from 'jspdf';
import html2canvas from 'html2canvas';

export default {
  data() {
    return {
      imageSrc: null
    }
  },
  methods: {
    previewImage(event) {
      const file = event.target.files[0];
      if (file) {
        this.imageSrc = URL.createObjectURL(file);
      }
    },
    async convertToPDF() {
    if (!this.imageSrc) return;

    const canvas = await html2canvas(this.$refs.image);
    const imgWidth = canvas.width;
    const imgHeight = canvas.height;

    // A4 용지의 크기 (210mm x 297mm)를 포인트로 변환 (1mm = 2.83 포인트)
    const a4Width = 210 * 2.83;
    const a4Height = 297 * 2.83;

    // 이미지의 가로/세로 비율을 유지하면서 A4 용지의 가로 크기에 맞게 조정
    let scaledWidth = a4Width;
    let scaledHeight = (a4Width / imgWidth) * imgHeight;

    // 이미지가 A4 용지의 세로 크기를 초과하는 경우, 세로 크기를 기준으로 조정
    if (scaledHeight > a4Height) {
        scaledWidth = (a4Height / imgHeight) * imgWidth;
        scaledHeight = a4Height;
    }

    const pdf = new jsPDF('p', 'pt', [a4Width, a4Height]);
    pdf.addImage(canvas.toDataURL('image/png'), 'PNG', 0, 0, scaledWidth, scaledHeight);
    pdf.save('converted-image.pdf');
}

  }
}
</script> -->


<template>
  <div>
    <input type="file" @change="previewImage">
    <button @click="convertToPDF">Convert to PDF</button>
    <div>
      <img v-if="imageSrc" :src="imageSrc" alt="Preview" ref="image" :style="imageStyle" @load="setImageDimensions">
    </div>
  </div>
</template>

<script>
import { jsPDF } from 'jspdf';
import html2canvas from 'html2canvas';

export default {
  data() {
    return {
      imageSrc: null,
      originalWidth: 0,
      originalHeight: 0
    }
  },
  computed: {
    imageStyle() {
      // A4의 4분의 1 크기 (105mm x 148.5mm)를 포인트로 변환
      const quarterA4WidthPx = 105 * 2.83;
      const quarterA4HeightPx = 148.5 * 2.83;
      return {
        width: `${quarterA4WidthPx}px`,
        height: `${quarterA4HeightPx}px`,
        objectFit: 'contain'
      };
    }
  },
  methods: {
    previewImage(event) {
      const file = event.target.files[0];
      if (file) {
        this.imageSrc = URL.createObjectURL(file);
      }
    },
    setImageDimensions(event) {
      this.originalWidth = event.target.naturalWidth;
      this.originalHeight = event.target.naturalHeight;
    },
    // async convertToPDF() {
    //   if (!this.imageSrc) return;

    //   const imgWidth = this.originalWidth;
    //   const imgHeight = this.originalHeight;

    //   // A4 용지의 크기 (210mm x 297mm)를 포인트로 변환 (1mm = 2.83 포인트)
    //   const a4Width = 210 * 2.83;
    //   const a4Height = 297 * 2.83;

    //   // 이미지의 가로/세로 비율을 유지하면서 A4 용지의 가로 크기에 맞게 조정
    //   let scaledWidth = a4Width;
    //   let scaledHeight = (a4Width / imgWidth) * imgHeight;

    //   // 이미지가 A4 용지의 세로 크기를 초과하는 경우, 세로 크기를 기준으로 조정
    //   if (scaledHeight > a4Height) {
    //     scaledWidth = (a4Height / imgHeight) * imgWidth;
    //     scaledHeight = a4Height;
    //   }



    //   const canvas = await html2canvas(this.$refs.image);
    //   const pdf = new jsPDF('p', 'pt', [a4Width, a4Height]);
    //   pdf.addImage(canvas.toDataURL('image/png'), 'PNG', 0, 0, scaledWidth, scaledHeight);
    //   pdf.save('converted-image.pdf');
    // }
    async convertToPDF() {
      if (!this.imageSrc) return;

      const imgWidth = this.originalWidth;
      const imgHeight = this.originalHeight;

      // A4 용지의 크기 (210mm x 297mm)를 포인트로 변환 (1mm = 2.83 포인트)
      const a4Width = 210 * 2.83;
      const a4Height = 297 * 2.83;

      // 이미지의 가로/세로 비율을 유지하면서 A4 용지의 가로 크기에 맞게 조정
      let scaledWidth = a4Width;
      let scaledHeight = (a4Width / imgWidth) * imgHeight;

      // 이미지가 A4 용지의 세로 크기를 초과하는 경우, 세로 크기를 기준으로 조정
      if (scaledHeight > a4Height) {
        scaledWidth = (a4Height / imgHeight) * imgWidth;
        scaledHeight = a4Height;
      }

      // Canvas 생성 및 이미지 그리기
      const canvas = document.createElement('canvas');
      canvas.width = imgWidth;
      canvas.height = imgHeight;
      const ctx = canvas.getContext('2d');
      ctx.drawImage(this.$refs.image, 0, 0, imgWidth, imgHeight);

      const pdf = new jsPDF('p', 'pt', [a4Width, a4Height]);
      pdf.addImage(canvas.toDataURL('image/png'), 'PNG', 0, 0, scaledWidth, scaledHeight);
      pdf.save('converted-image.pdf');
    }


  }
}
</script>
