!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grocery List</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
    <style>
        body {
            background-color: #f5f5f5;
        }
        .list-item {
            padding: 10px;
            border-bottom: 1px solid #ccc;
        }
        .list-item:last-child {
            border-bottom: none;
        }
        .purchased {
            text-decoration: line-through;
        }
    </style>
</head>
<body>
    <div class="container mt-5">
        <h1>Grocery List</h1>
        <button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#addItemModal">Add Item</button>
        <button class="btn btn-danger" id="clear-all-btn">Clear All</button>
        <div class="row mt-3">
            <div class="col-md-6">
                <input type="search" id="search-input" class="form-control" placeholder="Search items">
            </div>
            <div class="col-md-6">
                <p id="list-stats">Total items: <span id="total-items">0</span>, Purchased items: <span id="purchased-items">0</span></p>
            </div>
        </div>
        <ul id="grocery-list" class="list-group mt-3">
        </ul>
    </div>

    <!-- Add Item Modal -->
    <div class="modal fade" id="addItemModal" tabindex="-1" aria-labelledby="addItemModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="addItemModalLabel">Add Item</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <input type="text" id="item-name" class="form-control" placeholder="Item name">
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                    <button type="button" class="btn btn-primary" id="add-item-btn">Add Item</button>
                </div>
            </div>
        </div>
    </div>

    
    <div class="modal fade" id="editItemModal" tabindex="-1" aria-labelledby="editItemModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="editItemModalLabel">Edit Item</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <input type="text" id="edit-item-name" class="form-control" placeholder="Item name">
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                    <button type="button" class="btn btn-primary" id="save-item-changes-btn">Save Changes</button>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-ka7Sk0Gln4gmtz2MlQnikT1wXgYsOg+OMhuP+IlRH9sENBO0LRn5q+8nbTov4+1p" crossorigin="anonymous"></script>
    <script>
        let groceryList = [];
        const listElement = document.getElementById('grocery-list');
        const itemNameInput = document.getElementById('item-name');
        const addItemBtn = document.getElementById('add-item-btn');
        const clearAllBtn = document.getElementById('clear-all-btn');
        const searchInput = document.getElementById('search-input');
        const totalItemsSpan = document.getElementById('total-items');
        const purchasedItemsSpan = document.getElementById('purchased-items');
        const editItemModal = document.getElementById('editItemModal');
        const editItemNameInput = document.getElementById('edit-item-name');
        const saveItemChangesBtn = document.getElementById('save-item-changes-btn');
        let editingItemIndex = null;

        addItemBtn.addEventListener('click', () => {
            const itemName = itemNameInput.value.trim();
            if (itemName) {
                groceryList.push({ name: itemName, purchased: false });
                updateList();
                itemNameInput.value = '';
            }
        });

        clearAllBtn.addEventListener('click', () => {
            groceryList = [];
            updateList();
        });

        searchInput.addEventListener('input', () => {
            const searchValue = searchInput.value.toLowerCase();
            const listItems = listElement.children;
            for (let i = 0; i < listItems.length; i++) {
                const listItem = listItems[i];
                const itemNameSpan = listItem.querySelector('span');
                if (itemNameSpan.textContent.toLowerCase().includes(searchValue)) {
                    listItem.style.display = 'block';
                } else {
                    listItem.style.display = 'none';
                }
            }
        });

        saveItemChangesBtn.addEventListener('click', () => {
            if (editingItemIndex !== null) {
                const newitemName = editItemNameInput.value.trim();
                if (newitemName) {
                    groceryList[editingItemIndex].name = newitemName;
                    updateList();
                    editItemModal.modal('hide');
                    editingItemIndex = null;
                }
            }
        });

        function updateList() {
            listElement.innerHTML = '';
            let totalItems = 0;
            let purchasedItems = 0;
            groceryList.forEach((item, index) => {
                totalItems++;
                if (item.purchased) {
                    purchasedItems++;
                }
                const listItem = document.createElement('li');
                listItem.classList.add('list-group-item', 'list-item');
                const checkbox = document.createElement('input');
                checkbox.type = 'checkbox';
                checkbox.checked = item.purchased;
                checkbox.addEventListener('change', () => {
                    item.purchased = checkbox.checked;
                    updateList();
                });
                const itemNameSpan = document.createElement('span');
                itemNameSpan.textContent = item.name;
                if (item.purchased) {
                    itemNameSpan.classList.add('purchased');
                }
                const editBtn = document.createElement('button');
                editBtn.classList.add('btn', 'btn-secondary', 'float-end', 'me-2');
                editBtn.textContent = 'Edit';
                editBtn.addEventListener('click', () => {
                    editingItemIndex = index;
                    editItemNameInput.value = item.name;
                    editItemModal.modal('show');
                });
                const removeBtn = document.createElement('button');
                removeBtn.classList.add('btn', 'btn-danger', 'float-end');
                removeBtn.textContent = 'Remove';
                removeBtn.addEventListener('click', () => {
                    groceryList.splice(index, 1);
                    updateList();
                });
                listItem.appendChild(checkbox);
                listItem.appendChild(itemNameSpan);
                listItem.appendChild(editBtn);
                listItem.appendChild(removeBtn);
                listElement.appendChild(listItem);
            });
            totalItemsSpan.textContent = totalItems;
            purchasedItemsSpan.textContent = purchasedItems;
        }
    </script>
</body>
</html>
