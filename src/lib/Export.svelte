<script lang="ts">
    import jsPDF from "jspdf";
    import "jspdf-autotable";
    import { FileText, Download } from "lucide-svelte";

    export let names: string[] = [];
    export let groups: string[][] = [];

    function exportToTxt() {
        let content = "Daftar Nama:\n";
        content += names.join("\n");

        if (groups.length > 0) {
            content += "\n\nHasil Grup:\n";
            groups.forEach((group, i) => {
                content += `\nGrup ${i + 1}:\n`;
                content += group.join("\n");
            });
        }

        const blob = new Blob([content], { type: "text/plain;charset=utf-8" });
        const link = document.createElement("a");
        link.href = URL.createObjectURL(blob);
        link.download = "daftar-nama.txt";
        link.click();
        URL.revokeObjectURL(link.href);
    }

    function exportToPdf() {
        const doc = new jsPDF();

        doc.setFontSize(18);
        doc.text("Utilitas Daftar Nama", 14, 22);

        // @ts-ignore
        doc.autoTable({
            startY: 30,
            head: [["No.", "Nama"]],
            body: names.map((name, i) => [i + 1, name]),
            theme: "grid",
            styles: { fontSize: 10 },
            headStyles: { fillColor: [44, 62, 80] },
        });

        if (groups.length > 0) {
            let finalY = (doc as any).lastAutoTable.finalY || 40;
            doc.addPage();
            doc.setFontSize(16);
            doc.text("Hasil Grup", 14, 20);

            let yPos = 30;
            groups.forEach((group, i) => {
                // @ts-ignore
                doc.autoTable({
                    startY: yPos,
                    head: [[`Grup ${i + 1}`]],
                    body: group.map((name) => [name]),
                    theme: "striped",
                    headStyles: { fillColor: [52, 152, 219] },
                    didDrawPage: (data) => {
                        yPos = data.cursor.y;
                    },
                });
                yPos = (doc as any).lastAutoTable.finalY + 10;
            });
        }

        doc.save("daftar-nama.pdf");
    }
</script>

<div class="space-y-2">
    <h2 class="card-title text-lg">Ekspor Hasil</h2>
    <div class="flex gap-2 flex-wrap">
        <button class="btn btn-outline btn-sm btn-icon" on:click={exportToTxt}>
            <FileText size={16} />
            Unduh .txt
        </button>
        <button
            class="btn btn-outline btn-secondary btn-sm btn-icon"
            on:click={exportToPdf}
        >
            <Download size={16} />
            Unduh .pdf
        </button>
    </div>
</div>
