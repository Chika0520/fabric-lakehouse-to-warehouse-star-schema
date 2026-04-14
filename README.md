# Project 2: Lakehouse-to-Warehouse SCD2 Star Schema in Microsoft Fabric

## Project Overview
This project demonstrates how I built a Lakehouse-to-Warehouse reporting solution in Microsoft Fabric using a structured dimensional modeling approach. The raw customer master and sales transaction files were first stored in Azure Blob Storage and connected to the Lakehouse through a shortcut. After the data was cleaned and standardized, it was stored as trusted Silver Delta tables in the Lakehouse.

To make this Silver data continuously available for downstream reporting, I created materialized lake views on top of the Silver tables. These materialized views provided an always-updated serving layer that could be used as the source for Warehouse modeling. Using these views, I then designed and built a Warehouse star schema consisting of `dim_date`, `dim_customer`, `dim_product`, and `fact_sales`.

The customer dimension was modeled using Slowly Changing Dimension Type 2 logic so that customer changes over time could be preserved correctly. This means that when customer attributes changed, the previous record was closed with an `EffectiveEndDate`, the new version was inserted with a new `EffectiveStartDate`, and the latest version was identified with a `CurrentFlag`. The fact table was then designed to join to the correct historical customer version based on the transaction date.

## Solution Flow
The project follows this flow:

1. Raw customer master and sales transaction files are stored in Azure Blob Storage.
2. A Lakehouse shortcut is used to access the Blob Storage files in Microsoft Fabric.
3. The data is cleaned, standardized, and loaded into Silver Delta tables in the Lakehouse.
4. Materialized lake views are created on top of the Silver tables to provide an always-updated source layer.
5. The Warehouse reads from these materialized lake views to build reporting-ready dimensions and facts.
6. A semantic model can then be created on top of the Warehouse for reporting, dashboards, and analytics.

## Warehouse Model
The Warehouse star schema includes:

- `dim_date`
- `dim_customer`
- `dim_product`
- `fact_sales`

### dim_customer
The customer dimension is designed with SCD Type 2 logic to preserve historical customer changes. It uses:
- `CustomerRecordID` as the surrogate key
- `CustomerID` as the business key
- `EffectiveStartDate`
- `EffectiveEndDate`
- `CurrentFlag`

### dim_product
The product dimension stores the reporting-ready product attributes and uses:
- `ProductRecordID` as the surrogate key
- `ProductID` as the business key

### dim_date
The date dimension supports reporting and time-based analysis by storing standard calendar attributes such as day, month, quarter, year, and weekday.

### fact_sales
The fact table stores the measurable transaction data and links to the dimensions through surrogate keys. It includes measures such as:
- `OrderQuantity`
- `UnitPriceAmount`
- `TaxAmount`

## Materialized Lake Views
A key part of this project is the use of materialized lake views. Since the Silver tables already existed in the Lakehouse as trusted Delta tables, I used materialized views as a way to expose an always-updated serving layer for the Warehouse. This provided a practical way to connect the Lakehouse and the Warehouse without rebuilding the Silver logic inside the Warehouse.

## Source-to-Target Mapping
This project also emphasizes the importance of Source-to-Target Mapping documentation. The mapping document explains how source fields from the Silver tables are transformed into the final Warehouse dimensions and fact tables. This improves:
- traceability
- testing
- documentation quality
- communication between technical and business users

## Naming Standards and Classwords
Another important part of this project is the use of clear naming standards for dimensional modeling. I used classwords to make the model easier to read and easier to maintain. For example:
- surrogate keys use names like `CustomerRecordID` and `ProductRecordID`
- business identifiers use names like `CustomerID` and `ProductID`
- descriptive attributes use names like `CustomerFullName`, `CityName`, and `MembershipTierName`
- numeric measures use names like `UnitPriceAmount` and `TaxAmount`
- boolean fields use names like `CurrentFlag`

Using consistent naming standards improves model clarity and makes the reporting layer easier for others to understand.

## Technologies Used
- Microsoft Fabric
- Azure Blob Storage
- Lakehouse
- Materialized Lake Views
- Warehouse
- SQL
- PySpark
- Delta Tables
- Semantic Modeling

## Why This Project Matters
This project demonstrates how to design a modern reporting architecture in Microsoft Fabric by combining:
- cloud storage ingestion
- Lakehouse transformation
- materialized serving layers
- Warehouse dimensional modeling
- SCD Type 2 history tracking
- reporting and analytics enablement

It shows how trusted Lakehouse data can be turned into a reporting-ready Warehouse model that supports business intelligence, dashboards, and analytics.

## Outcome
The final result is a reporting-ready Warehouse star schema built from always-updated Lakehouse Silver data. The project demonstrates best practices in dimensional modeling, source-to-target mapping, naming standards, customer history tracking, and semantic-model-ready reporting design.


