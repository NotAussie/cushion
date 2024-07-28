# `🛋️` Cushion

> [!NOTE]
> Cushion is a port of Beanie, I haven't started work on it yet sadly.

> [!NOTE]
> Credit where credit is due, cushion is built on [Beanie ODM](https://github.com/BeanieODM/beanie) and uses Beanie source code.

## `📰` Overview

[Cushion ODM](https://github.com/NotAussie/cushion) - is an asynchronous Python object-document mapper (ODM) for [Apache CouchDB™](https://couchdb.apache.org/). Data models are based on [Pydantic](https://pydantic-docs.helpmanual.io/).

When using Cushion each database collection has a corresponding `Document` that
is used to interact with that collection. In addition to retrieving data,
Cushion allows you to add, update, or delete documents from the collection as
well.

Cushion saves you time by removing boilerplate code, and it helps you focus on
the parts of your app that actually matter.

Data and schema migrations are supported by Cushion out of the box.

## `📥` Installation

### `🗃️` PIP

```shell
pip install cushion
```

### `🖋️` Poetry

```shell
poetry add cushion
```

## `⚙️` Example

```python
import asyncio
from typing import Optional

from aiocouch import CouchDB
from pydantic import BaseModel

from cushion import Document, Indexed, init_cushion


class Category(BaseModel):
    name: str
    description: str


class Product(Document):
    name: str  # You can use normal types just like in pydantic
    description: Optional[str] = None
    price: Indexed(
        float
    )  # You can also specify that a field should correspond to an index
    category: Category  # You can include pydantic models as well


# This is an asynchronous example, so we will access it from an async function
async def example():
    # Cushion uses Motor async client under the hood
    async with CouchDB(
        "http://localhost:5984", user="admin", password="admin"
    ) as couchdb:

        # Initialize cushion with the Product document class

        await init_cushion(database=await couchdb["db_name"], document_models=[Product])

        chocolate = Category(
            name="Chocolate",
            description="A preparation of roasted and ground cacao seeds.",
        )
        # Cushion documents work just like pydantic models

        tonybar = Product(name="Tony's", price=5.95, category=chocolate)
        # And can be inserted into the database

        await tonybar.insert()

        # You can find documents with pythonic syntax

        product = await Product.find_one(Product.price < 10)

        # And update them

        await product.set({Product.name: "Gold bar"})


if __name__ == "__main__":
    asyncio.run(example())
```

----

Credit where credit is due, cushion is built on [Beanie ODM](https://github.com/BeanieODM/beanie) and uses Beanie source code.
