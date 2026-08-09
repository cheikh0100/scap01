<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>عرض جدول CSV من المستودع</title>
    <style>
        body { font-family: system-ui, -apple-system, sans-serif; padding: 20px; background-color: #f8f9fa; color: #333; }
        h2 { color: #2c3e50; margin-bottom: 20px; }
        .table-container { overflow-x: auto; box-shadow: 0 4px 6px rgba(0,0,0,0.1); border-radius: 8px; }
        table { width: 100%; border-collapse: collapse; background-color: white; }
        th, td { border: 1px solid #e0e0e0; padding: 12px 15px; text-align: right; }
        th { background-color: #007bff; color: white; font-weight: bold; }
        tr:nth-child(even) { background-color: #f8f9fa; }
        tr:hover { background-color: #f1f1f1; }
        #status { font-size: 16px; color: #666; font-weight: bold; margin-bottom: 15px; }
    </style>
</head>
<body>

    <h2>جدول البيانات المجلوب من المستودع</h2>
    <div id="status">جاري تحميل ملف data.csv...</div>

    <div class="table-container">
        <table id="csvTable" style="display: none;">
            <thead id="tableHead"></thead>
            <tbody id="tableBody"></tbody>
        </table>
    </div>

    <script>
        // اسم ملف CSV المخزن في المستودع
        const csvFilePath = 'fl_criteria.csv';

        // جلب الملف عند فتح الصفحة
        fetch(csvFilePath)
            .then(response => {
                if (!response.ok) {
                    throw new Error('تعذر العثور على ملف data.csv في المستودع.');
                }
                return response.text();
            })
            .then(csvText => {
                document.getElementById('status').style.display = 'none';
                document.getElementById('csvTable').style.display = 'table';
                generateTable(csvText);
            })
            .catch(error => {
                document.getElementById('status').textContent = 'خطأ: ' + error.message;
                document.getElementById('status').style.color = 'red';
            });

        function generateTable(csvText) {
            const lines = csvText.trim().split('\n');
            const tableHead = document.getElementById('tableHead');
            const tableBody = document.getElementById('tableBody');

            if (lines.length === 0) return;

            // إنشاء عناوين الأعمدة من الصف الأول
            const headers = lines[0].split(',');
            const headRow = document.createElement('tr');
            headers.forEach(header => {
                const th = document.createElement('th');
                th.textContent = header.trim();
                headRow.appendChild(th);
            });
            tableHead.appendChild(headRow);

            // إنشاء صفوف البيانات
            for (let i = 1; i < lines.length; i++) {
                if (!lines[i].trim()) continue;
                
                const rowData = lines[i].split(',');
                const row = document.createElement('tr');

                rowData.forEach(cell => {
                    const td = document.createElement('td');
                    td.textContent = cell.trim();
                    row.appendChild(td);
                });

                tableBody.appendChild(row);
            }
        }
    </script>

</body>
</html>
