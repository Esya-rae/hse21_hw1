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
(локально) scp -P 32222 -i bioinf asraevskaya@92.242.58.92:~/multiqc/multiqc_report.html ~/bio_minor/multiqc
 Отчеты:
 ![Screenshot from 2021-10-27 21-48-35](https://user-images.githubusercontent.com/57015713/139128499-9e1313e5-b146-4ebe-bc11-f99a451bd2f6.png)
8. Теперь подрезаем по качеству с помощью platanus_trim и platanus_internal_trim + удаляем праймеры:
platanus_trim pair_R1.fastq pair_R2.fastq 
platanus_internal_trim mate_R1.fastq mate_R2.fastq
9. Удалим уже ненужные fastq  файлы:
rm -r *fastq
10. Теперь аналогично пунктам 5-7 оценим качество новых чтений:
mkdir trimmed_fastqc
ls  pair* mate* | xargs -P 4 -tI{} fastqc -o trimmed_fastqc {}
mkdir trimmed_multiqc
multiqc -o trimmed_multiqc trimmed_fastqc
(локально) scp -p 32222 -i bioinf asraevskaya@92.242.58.92:~/trimmed_multiqc/multiqc_report.html ~/bio_minor/trimmed_multiqc
Отчеты:
11. Собираем контиги:
platanus assemble -o Poil -f pair_R1.fastq.trimmed pair_R2.fastq.trimmed 2> assemble.log
(локально) scp -P 32222 -i bioinf asraevskaya@92.242.58.92:~/Poil_contig.fa ~/bio_minor/
12. Анализ контигов:
13. Собираем скаффолды:
platanus scaffold -o Poil -t 1 -c Poil_contig.fa -IP1 pair_R1.fastq.trimmed pair_R2.fastq.trimmed -OP2 mate_R1.fastq.int_trimmed mate_R2.fastq.int_trimmed 2> scaffold.log
14.  Уменьшаем количество гэпов и скачиваем
platanus gap_close -o Poil -t 1 -c Poil_scaffold.fa -IP1 pair_R1.fastq.trimmed pair_R2.fastq.trimmed -OP2 mate_R1.fastq.int_trimmed mate_R2.fastq.int_trimmed 2> gapclose.log
(локально) scp -P 32222 -i bioinf asraevskaya@92.242.58.92:~/Poil_scaffold.fa ~/bio_minor/

