step 1 : use id 
```bash
<table id="table_sort" border="1" width="100%">
```

step 2: style
```bash
#table_sort th{
    cursor:pointer;
    user-select:none;
    position:relative;
}

.sort-arrows{
    display:inline-flex;
    flex-direction:column;
    margin-left:6px;
    font-size:10px;
    line-height:8px;
}

.sort-arrows span{
    color:#ccc;
}

.sort-arrows .active{
    color:#000;
}
```

step 3 : javascript

```bash
document.addEventListener("DOMContentLoaded", function () {

    const table = document.getElementById("table_sort");
    const headers = table.querySelectorAll("th");
    const tbody = table.querySelector("tbody");

    headers.forEach((header, index) => {

        // arrow add by JS
        const arrows = document.createElement("span");
        arrows.className = "sort-arrows";
        arrows.innerHTML = `
            <span class="arrow-up">▲</span>
            <span class="arrow-down">▼</span>
        `;
        header.appendChild(arrows);

        let asc = true;

        header.addEventListener("click", () => {

            const rows = Array.from(tbody.querySelectorAll("tr"));

            rows.sort((a, b) => {

                let A = a.children[index].innerText.trim();
                let B = b.children[index].innerText.trim();

                if (!isNaN(A) && !isNaN(B)) {
                    return asc ? A - B : B - A;
                }

                return asc ? A.localeCompare(B) : B.localeCompare(A);
            });

            rows.forEach(row => tbody.appendChild(row));

            // reset arrows
            document.querySelectorAll(".sort-arrows span").forEach(el => el.classList.remove("active"));

            // activate arrow
            if (asc) {
                header.querySelector(".arrow-up").classList.add("active");
            } else {
                header.querySelector(".arrow-down").classList.add("active");
            }

            asc = !asc;

        });

    });

});
