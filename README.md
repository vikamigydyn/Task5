## Лабораторная работа №5. Хранение данных. Настройки и внешние файлы
## Выполнила: Мигыдын Вика, ИСП-211о

## 1. Обзор
Приложение предназначено для загрузки и просмотра PDF файлов журналов по их идентификатору. Пользователь может ввести номер журнала, скачать его, просмотреть и удалить загруженный файл.

## 2. Структура
- **MainActivity**: Основная активность, содержащая элементы управления для ввода номера журнала, кнопки для загрузки, просмотра и удаления файла, а также отображение статуса загрузки.

![7](https://github.com/user-attachments/assets/54a43d41-e864-43a0-b548-b4f5cabc6643)

## 3. MainActivity
На данном экране расположены кнопки:
- Кнопка "Скачать": При нажатии запускает процесс загрузки файла по введенному идентификатору.


`java
downloadButton.setOnClickListener(v -> {
    String journalId = journalIdInput.getText().toString();
    if (!journalId.isEmpty()) {
        createDirectoryAndDownload(journalId);
    } else {
        statusText.setText("Пожалуйста, введите номер журнала!");
    }
});'


![2](https://github.com/user-attachments/assets/44affca4-2da5-4327-b16e-bf9c2c7a33b1)

![3](https://github.com/user-attachments/assets/08ecdedf-a0d7-4237-b8e8-fba87fadc584)

![6](https://github.com/user-attachments/assets/27a01913-05c2-4492-955f-bb1a7df0ebdb)

- Кнопка "Смотреть": Позволяет открыть загруженный PDF файл, если он существует.
  
'viewButton.setOnClickListener(v -> {
    if (downloadedFile != null && downloadedFile.exists()) {
        Uri fileUri = FileProvider.getUriForFile(MainActivity.this, getApplicationContext().getPackageName() + ".provider", downloadedFile);
        Intent viewIntent = new Intent(Intent.ACTION_VIEW);
        viewIntent.setDataAndType(fileUri, "application/pdf");
        viewIntent.setFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
        try {
            startActivity(Intent.createChooser(viewIntent, "Открыть PDF в приложении"));
        } catch (ActivityNotFoundException e) {
            Toast.makeText(MainActivity.this, "Не установлено приложение для просмотра PDF-файлов", Toast.LENGTH_SHORT).show();
        }
    }
});'

![4](https://github.com/user-attachments/assets/ac3a218e-e472-42c8-a91b-1045cfdead07)

- Кнопка "Удалить": Удаляет загруженный файл, если он существует.
'deleteButton.setOnClickListener(v -> {
    if (downloadedFile != null && downloadedFile.exists()) {
        if (downloadedFile.delete()) {
            viewButton.setEnabled(false);
            deleteButton.setEnabled(false);
            statusText.setText("Файл удален");
        }
    }
});'

![5](https://github.com/user-attachments/assets/18e3495a-ede9-4457-b7fa-4a757e8fce64)

## 4. Загрузка файла
Загрузка файла осуществляется асинхронно с использованием AsyncTask. Приложение создает директорию для хранения загруженных файлов и загружает PDF по указанному URL.

'private void createDirectoryAndDownload(String journalId) {
    File dir = new File(getExternalFilesDir(null), "JournalDownloads");
    if (!dir.exists()) {
        boolean isCreated = dir.mkdirs();
        if (isCreated) {
            Log.d("Directory", "Directory created successfully");
        } else {
            Log.d("Directory", "Failed to create directory");
            Toast.makeText(this, "Ошибка при создании директории!", Toast.LENGTH_SHORT).show();
            return;
        }
    }
    String fileUrl = "https://ntv.ifmo.ru/file/journal/" + journalId + ".pdf";
    new DownloadFileTask(MainActivity.this).execute(fileUrl, journalId);
}'

## 5. Дополнительные функции
Всплывающее окно с инструкциями: Появляется при первом запуске приложения, с возможностью отключить его отображение в будущем.

![1 (2)](https://github.com/user-attachments/assets/c94dbcae-9809-4c89-bc0d-c4a0d02c5e6f)

