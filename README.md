![Screenshot from 2021-10-27 21-46-25](https://user-images.githubusercontent.com/57015713/139127912-e005660c-c5b5-4ff2-b874-f9c934a97a3d.png)
![Screenshot from 2021-10-27 21-48-08](https://user-images.githubusercontent.com/57015713/139128400-a44a3913-d671-477a-8778-5a5f0dcdb4f8.png)
#  Домашнее задание 1
1. Подключение к серверу:
ssh asraevskaya@92.242.58.92 -p 32222 -i bioinf
2. Создаем ссылочки:
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq        
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq 
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
3. Выбираем случайные чтения:
seqtk sample -s802 oil_R1.fastq 5000000 > pair_R1.fastq
seqtk sample -s802 oil_R2.fastq 5000000 > pair_R2.fastq
seqtk sample -s802 oilMP_S4_L001_R1_001.fastq 1500000 > mate_R1.fastq
seqtk sample -s802 oilMP_S4_L001_R2_001.fastq 1500000 > mate_R2.fastq
4. Изначальные файлы нам больше не нужны, так что их удалим:
rm -r oil_R1.fastq
rm -r oil_R2.fastq
rm -r oilMP_S4_L001_R1_001.fastq
rm -r oilMP_S4_L001_R2_001.fastq
5. Оценим качество наших чтений с fastqc:
mkdir fastqc
ls pair* mate* | xargs -P 4 -tI{} fastqc -o fastqc {}
6. Аналогично с multiqc:
mkdir multiqc
multiqc -o multiqc fastqc
7. Теперь нужно скачать отчеты к себе:
scp -p 32222 -i bioinf asraevskaya@92.242.58.92:~/multiqc/multiqc_report.html ~/bio_minor/multiqc
 Отчеты:
 ![Screenshot from 2021-10-27 21-48-35](https://user-images.githubusercontent.com/57015713/139128499-9e1313e5-b146-4ebe-bc11-f99a451bd2f6.png)
8. Теперь подрезаем по качеству с помощью platanus_trim и platanus_internal_trim + удаляем праймеры:
platanus_trim pair_R1.fastq pair_R2.fastq 
platanus_internal_trim mate_R1.fastq mate_R2.fastq
9. Удалим уже ненужные fastq  файлы:
