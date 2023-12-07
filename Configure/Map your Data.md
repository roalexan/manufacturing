# Context

Data Manager for Manufacturing (DMM) utilizes the ISA 95 standard's ontology for structuring and integrating data points across manufacturing systems. DMM ensures interoperability and coherence within the data architecture.

Detailed descriptions of the ISA 95 ontology based DMM data models are delineated in the following sections:

-   [Common and Resource Models](../Appendix/DMM%20Data%20and%20Relationship%20Models%201%20of%203.pdf)

-   [Operational Models](../Appendix/DMM%20Data%20and%20Relationship%20Models%202%20of%203.pdf)

-   [Work Models](../Appendix/DMM%20Data%20and%20Relationship%20Models%203%20of%203.pdf)

Each document serves as a reference for various aspects of the manufacturing operation, from resources to work floor operations.

**Mapping process**

To standardize disparate data formats, DMM mandates the alignment of customer data with the ISA 95 Standard Data Model. This process is essential for enabling seamless data flow and integration within manufacturing systems.

**Scenario: Non-standard Data Models**

In cases where the customer's data infrastructure lacks a formalized data model and is reliant on relational databases with interlinked foreign keys, DMM approaches the mapping as follows:

- **Identification:** Isolate individual data points within the customer's databases.

- **Categorization:** Classify each data point according to the ISA 95 data structure categories – namely, common and resource models, operational models, and work models.

- **Association:** Determine the relationships among data points, utilizing ISA 95's relational framework to align with foreign keys within the customer's existing databases.

- **Transformation:** Translate relationships between data points into the standardized ISA 95 format, ensuring that all data points are consistently defined and connected as per the standard ontology.

## Example 1: Production Scheduling

Consider a customer-provided data excerpt from a Manufacturing Execution System (MES) depicted in a simplified tabular format:

| Order Number | Scheduled quantity | UOM | Start date | End Date | WorkCenter      | Product Code  |
|--------------|--------------------|-----|------------|----------|-----------------|---------------|
| P129391      | 1500               | Kg  | 2/9/2023   | 3/9/2023 | BREADBLAST-OV04 | BAGGBTR680G69 |

This dataset represents a planned manufacturing schedule. The mapping of this data to the ISA 95 data model is guided by the Operations Schedule model, inside [Operational models](../Appendix/DMM%20Data%20and%20Relationship%20Models%202%20of%203.pdf)

![the image shows the operations schedule information model](../media/operations-schedule-information.png)
Using the MES data example provided, each column will be mapped to the most appropriate entity within the DMM ISA 95 data model.

**Column 1: Order Number**

| Order Number | Scheduled quantity | UOM | Start date | End Date | WorkCenter      | Product Code  |
|--------------|--------------------|-----|------------|----------|-----------------|---------------|
| P129391      | 1500               | Kg  | 2/9/2023   | 3/9/2023 | BREADBLAST-OV04 | BAGGBTR680G69 |

Depending on the structure of the customers operations, a production order could be an "Operations Schedule" or "Operations Request" (see the differences in Operations Schedule model, inside [Operational models]((../Appendix/DMM%20Data%20and%20Relationship%20Models%202%20of%203.pdf))). In this case, we will map it to "Operations Request".

Since Order number and the value (P129391) seem to refer to an ID, and the first column in Operations Request DMM entity is ID, we can map both.

![the image shows the operations schedule ID attribute](../media/ops-request-id-attribute.png)


> [!NOTE]
> Final map for the first column will be:
> 
> Order Number : OperationsRequest.id

**Column 2: Scheduled quantity**

| Order Number | Scheduled quantity | UOM | Start date | End Date | WorkCenter      | Product Code  |
|--------------|--------------------|-----|------------|----------|-----------------|---------------|
| P129391      | 1500               | Kg  | 2/9/2023   | 3/9/2023 | BREADBLAST-OV04 | BAGGBTR680G69 |

Scheduled quantity, in ISA 95 ontology, is part of the "material requirement" entity, which is linked to our Operations Request entity through an intermediate entity, Segment requirement.

Here is the path:  
![the image shows the operations schedule path to material requirement](../media/ops-request-material-requirement-path.png)

In "Material Requirement" entity, you can find an attribute called "Quantity".

Note that for this mapping, we need to also define a specific attribute that will set this value to be produced, consumed, or any of the given "enums" as per ISA 95:

![the image shows the material use enum](../media/material-use-enum.png)

For this example, we need to set this value as "Material produced", as the table is showing the required quantity to be manufactured.
> [!NOTE]
> The final map for the second column will be:
> 
>Scheduled quantity: MaterialRequirement.{MaterialUse:"Material produced"}.quantity

**Column 3: UOM**

| Order Number | Scheduled quantity | UOM | Start date | End Date | WorkCenter      | Product Code  |
|--------------|--------------------|-----|------------|----------|-----------------|---------------|
| P129391      | 1500               | Kg  | 2/9/2023   | 3/9/202  | BREADBLAST-OV04 | BAGGBTR680G69 |

UOM (unit of measure), which pertains to the mapped quantity, is also an attribute of the "Material requirement" entity in DMM, the attribute is "Quantity unit of measure"
> [!NOTE]
> The final map for the third column will be:
> 
>UOM : MaterialRequirement.{MaterialUse:"Material produced"}.QuantityUnitOfMeasure

**Columns 4 and 5: Start date and End Date**

| Order Number | Scheduled quantity | UOM | Start date | End Date | WorkCenter      | Product Code  |
|--------------|--------------------|-----|------------|----------|-----------------|---------------|
| P129391      | 1500               | Kg  | 2/9/2023   | 3/9/202  | BREADBLAST-OV04 | BAGGBTR680G69 |

Start date and End Date refers to the planned dates for this specific order. In DMM, those are attributes of the "Operations Request" entity as well.

![the image shows the start and end attributes of Operations request](../media/ops-request-startend-attributes.png)

> [!NOTE]
> The final map for columns 4 and 5 will be:
> 
> Start date : OperationsRequest.Starttime
> 
> End Date : OperationsRequest.Endtime

**Column 6: WorkCenter**

| Order Number | Scheduled quantity | UOM | Start date | End Date | WorkCenter      | Product Code  |
|--------------|--------------------|-----|------------|----------|-----------------|---------------|
| P129391      | 1500               | Kg  | 2/9/2023   | 3/9/202  | BREADBLAST-OV04 | BAGGBTR680G69 |

For WorkCenter, which seems to be the planned equipment where we will manufacture the required product, we again must follow a similar path like we did with the planned quantity, since the required equipment is not a direct attribute of "Operations Request" but a linked entity through, in this case, "Segment requirement", and "Equipment requirement".

![the image shows the path between equipment and ops request](../media/ops-request-equipment-path.png)

In Equipment model, you find the "Equipment" entity and take the ID from there.

> [!TIP]
> Although the path we follow in ISA 95 Ontology is not needed for the mapping itself, later in the process, we will need to define the relationship for this specific entity. That is why it is important to understand the path to access each entity.

> [!NOTE]
> The final map for column 6 will be:
>
> WorkCenter : Equipment.ID

**Column 7: Product code**

| Order Number | Scheduled quantity | UOM | Start date | End Date | WorkCenter      | Product Code  |
|--------------|--------------------|-----|------------|----------|-----------------|---------------|
| P129391      | 1500               | Kg  | 2/9/2023   | 3/9/202  | BREADBLAST-OV04 | BAGGBTR680G69 |

Product Code is the specific denomination of the customer's planned product.

Following the same path that we used for Scheduled quantity, we will go to "Material requirement", and from there we will map to ID. Note that for Material requirement we will always need to specify the enum (MaterialUse:"Material produced")

> [!NOTE]
> The final map for column 7 will be:
>
> Product Code : MaterialRequirement.id.{MaterialUse:"Material produced"}

We have completed the mapping of a Production scheduling simplified set of data.

## Example 2: Production Reporting

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|
| P129391      | 1511     | Kg  | 2/9/2023         | 1     | XY234 | BREADBLAST-OV04 | BAGGBTR680G69 |

This dataset represents a manufacturing report of an actual production response. The mapping of this data to the ISA 95 data model is guided by the Operations Performance model from [Operational Models]((../Appendix/DMM%20Data%20and%20Relationship%20Models%202%20of%203.pdf)).

![the image shows the Operations Performance model](../media/operations-performance-information.png)

Using the MES data example provided, each column will be mapped to the most appropriate entity within the DMM ISA 95 data model.

**Column 1, Order Number:**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|
| P129391      | 1511     | Kg  | 2/9/2023         | 1     | XY234 | BREADBLAST-OV04 | BAGGBTR680G69 |

Same as in the last example, this is mapped to "Operations request".

> [!NOTE]
> The final map for column 1 will be:
>
> Order Number : OperationsRequest.id

**Column 2, quantity:**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|
| P129391      | 1511     | Kg  | 2/9/2023         | 1     | XY234 | BREADBLAST-OV04 | BAGGBTR680G69 |

In this case, the quantity refers to actual manufactured quantity, following this path:

In "Material Actual", part of PDF 2, we can find the attribute "quantity".

Just like we did with "Material requirement", we also need to specify the enum for "Material actual".

> [!NOTE]
> The final map for column 2 will be:
>
> quantity : MaterialActual.{MaterialUse:"Material produced"}.Quantity

**Column 3: UOM**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|
| P129391      | 1511     | Kg  | 2/9/2023         | 1     | XY234 | BREADBLAST-OV04 | BAGGBTR680G69 |

> [!NOTE]
> The final map for column 3 will be:
>
> UOM : MaterialActual.{MaterialUse:"Material produced"}.QuantityUnitOfMEasure

**Column 4: Created on local**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|
| P129391      | 1511     | Kg  | 2/9/2023         | 1     | XY234 | BREADBLAST-OV04 | BAGGBTR680G69 |

The first thing we need to notice is that this timestamp refers to the moment this record was created. So, this does not map with the "Operations Request" timestamps (Starttime/Endtime). Then, where can we find the mapping to this timestamp? The answer is "Segment Response".

![this image shows the postingdate attribute in Segment Response.](../media/segment-response-postingdate-attribute.png)

There are three different attributes with timestamp; the first two refer to when the current operation started and ended, while the third one refers to when the actual report was recorded/posted. (Note that, during production, many different segment responses may be reported against the same Production order).

> [!NOTE]
> The final map for column 4 will be:
>
> Created on local : SegmentResponse.PostingDate

**Column 5: Shift**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|
| P129391      | 1511     | Kg  | 2/9/2023         | 1     | XY234 | BREADBLAST-OV04 | BAGGBTR680G69 |

Shift will be mapped as "Segment Data", within the same Operations Performance group:

![the image shows the path between ops request and segment data](../media/ops-request-segment-data-path.png)

Segment data allows us to define any information that is linked to a specific Segment Response.

![the image shows the value attribute in segment data](../media/segment-data-vale-attribute.png)

> [!NOTE]
> The final map for column 5 will be:
>
> Shift : SegmentData.Value

**Column 6: Lotno**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|
| P129391      | 1511     | Kg  | 2/9/2023         | 1     | XY234 | BREADBLAST-OV04 | BAGGBTR680G69 |

For Lotno, we will use the entity "Material lot", which is linked to the Segment response as per:

![the image shows the path between segment response and material](../media/segment-response-material-path.png)

Note the path traverses "Material Actual", we will need this when defining the relationships.

In the Material model (PDF 1), we can find "Material lot" attribute ID:

![the image shows the attribute ID in material lot](../media/material-lot-id-value.png)

> [!NOTE]
> The final map for column 6 will be:
>
> LotNo : MaterialLot.ID

**Column 7: WorkCenter**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|
| P129391      | 1511     | Kg  | 2/9/2023         | 1     | XY234 | BREADBLAST-OV04 | BAGGBTR680G69 |

This is a good example of how the overall difference between two of the most common ISA 95 entity groups (Operations Schedule and Operations Performance) work.

In the previous example we had the very same column name and value, Workcenter \| BREADBLAST-OV04. The difference is, while the former was a planning requirement (the planner specified that this production order needs to be manufactured in this specific WorkCenter), in this case the field WorkCenter is part of a production report, meaning that this is the equipment that was used during manufacturing. Therefore, note that both equipments can be different (because of a maintenance problem, or due to manufacturing operations balance).

Therefore, in our production reporting scenario, we will map it to "Equipment" through "Equipment Actual".

> [!NOTE]
> The final map for column 7 will be:
>
> WorkCenter : Equipment.id

Note that, although this mapping looks like mapping for WorkCenter in Example 1, the actual "Equipment" linked to "Equipment Actual" might be different, for the reasons explained earlier.

**Column 8: Product Code**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|
| P129391      | 1511     | Kg  | 2/9/2023         | 1     | XY234 | BREADBLAST-OV04 | BAGGBTR680G69 |

Following the same path as Column 2 (quantity), we map this to:

> [!NOTE]
> The final map for column 8 will be:
>
> Product Code : MaterialActual.id. {MaterialUse:"Material produced"}

## Example 3: Quality Reporting

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  | Reason Description |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|--------------------|
| P129391      | 56       | Kg  | 2/9/2023         | 1     | SC002 | BREADBLAST-OV04 | BAGGBTR680G69 | Consistency loss   |

This dataset represents a manufacturing report of an actual production quality response. The mapping of this data to the ISA 95 data model is guided by the Operations test and Operations performance models from [Operational Models](\l).

As per ISA 95 highly normalized ontology, there are many ways to define scrap and other quality related events.

We will simplify as much as possible, following the current strategy:

We first define any Quality-related event as "Test Result". This Test Result will be linked –in the image above – to a "Testable Object". Testable objects in ISA 95 can be any object from [Resource models]((../Appendix/DMM%20Data%20and%20Relationship%20Models%201%20of%203.pdf)); in this case, our Testable Object is a "Material lot".

![the image shows relationship between test result and its testable object](../media/test-result-testable-object-relationship.png)

Finally, that material lot is linked to a "Material Actual", with a specific Enum of "inventoried", to consider that Scrapped lots are differentiated from those produced, or those consumed.

With this, we can proceed:

**Column 1: OrderNumber**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  | Reason Description |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|--------------------|
| P129391      | 56       | Kg  | 2/9/2023         | 1     | SC002 | BREADBLAST-OV04 | BAGGBTR680G69 | Consistency loss   |

> [!NOTE]
> The final map for column 1 will be:
>
> Order Number : OperationsRequest.id

**Column 2: quantity**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  | Reason Description |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|--------------------|
| P129391      | 56       | Kg  | 2/9/2023         | 1     | SC002 | BREADBLAST-OV04 | BAGGBTR680G69 | Consistency loss   |

Following our defined strategy, this quantity refers to a material actual (see last example's material actual diagram), with an Enum of "Inventoried".

> [!NOTE]
> The final map for column 2 will be:
>
> Quantity : MaterialActual.{MaterialUse:"Inventoried"}.Quantity

**Column 3: UOM**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  | Reason Description |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|--------------------|
| P129391      | 56       | Kg  | 2/9/2023         | 1     | SC002 | BREADBLAST-OV04 | BAGGBTR680G69 | Consistency loss   |

> [!NOTE]
> The final map for column 3 will be:
>
> UOM : MaterialActual.{MaterialUse:"Inventoried"}.QuantityUnitOfMEasure

**Column 4: Created on local**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  | Reason Description |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|--------------------|
| P129391      | 56       | Kg  | 2/9/2023         | 1     | SC002 | BREADBLAST-OV04 | BAGGBTR680G69 | Consistency loss   |

Since scrap reporting is part of the overall production reporting process, we will use the logic from Example 2 to map this column.

> [!NOTE]
> The final map for column 4 will be:
>
> Created on local : SegmentResponse.PostingDate

**Column 5: Shift**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  | Reason Description |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|--------------------|
| P129391      | 56       | Kg  | 2/9/2023         | 1     | SC002 | BREADBLAST-OV04 | BAGGBTR680G69 | Consistency loss   |

Same for shift:

> [!NOTE]
> The final map for column 5 will be:
>
> Shift : SegmentData.Value

**Column 6: Lotno**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  | Reason Description |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|--------------------|
| P129391      | 56       | Kg  | 2/9/2023         | 1     | SC002 | BREADBLAST-OV04 | BAGGBTR680G69 | Consistency loss   |

Lotno also will follow example 2 logic for mapping; just make sure that in this case the material actual that you link your material lot is the one pertaining to the scrapped material.

> [!NOTE]
> The final map for column 6 will be:
>
> Lotno : Materiallot.id

**Column 7: Workcenter**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  | Reason Description |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|--------------------|
| P129391      | 56       | Kg  | 2/9/2023         | 1     | SC002 | BREADBLAST-OV04 | BAGGBTR680G69 | Consistency loss   |

Same logic than in Example 2 for WorkCenter

> [!NOTE]
> The final map for column 7 will be:
>
> WorkCenter : Equipment.id

**Column 8: Product Code**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  | Reason Description |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|--------------------|
| P129391      | 56       | Kg  | 2/9/2023         | 1     | SC002 | BREADBLAST-OV04 | BAGGBTR680G69 | Consistency loss   |

Same logic as in Example 2 for Product Code, but in this case we will change the "enum" to differentiate the linked material actual (will use "inventoried" instead of Produced)

> [!NOTE]
> The final map for column 8 will be:
>
> Product Code : MaterialActual.id. {MaterialUse:"Material inventoried"}

**Column 9: Reason description**

| Order Number | quantity | UOM | Created on local | Shift | Lotno | WorkCenter      | Product Code  | Reason Description |
|--------------|----------|-----|------------------|-------|-------|-----------------|---------------|--------------------|
| P129391      | 56       | Kg  | 2/9/2023         | 1     | SC002 | BREADBLAST-OV04 | BAGGBTR680G69 | Consistency loss   |

This is the "Test Result" entity description:

> [!NOTE]
> The final map for column 9 will be:
>
> Reason description : TestResult.description

## Example 4: Downtime Reporting

| Order Number | WorkCenter      | Reason Description | DownTime Category   | DownTime StartUTC    | DownTime EndUTC      | Created on local | Shift |
|--------------|-----------------|--------------------|---------------------|----------------------|----------------------|------------------|-------|
| P129391      | BREADBLAST-OV04 | Water Supply Issue | Machine malfunction | 2/26/2023 4:38:13 PM | 2/26/2023 4:51:44 PM | 2/9/2023         | 1     |

This dataset represents a simplified manufacturing Downtime report of an actual process. The mapping of this data to the ISA 95 data model is guided by the Operations Event model, part of [Operational Models]((../Appendix/DMM%20Data%20and%20Relationship%20Models%202%20of%203.pdf)).

![the image showd the Operations Event model](../media/operations-event-information.png)

**Column 1: ID**

| ID      | WorkCenter      | Reason Description | DownTime Category   | DownTime StartUTC    | DownTime EndUTC      | Created on local | Shift |
|---------|-----------------|--------------------|---------------------|----------------------|----------------------|------------------|-------|
| P129391 | BREADBLAST-OV04 | Water Supply Issue | Machine malfunction | 2/26/2023 4:38:13 PM | 2/26/2023 4:51:44 PM | 2/9/2023         | 1     |

Every Downtime event will be defined per DMM data model as an "Operations Event"

> [!NOTE]
> The final map for column 1 will be:
> 
>ID : OperationsEvent.ID

**Columns 2,7,8: WorkCenter, Created on local and Shift**

| ID      | WorkCenter      | Reason Description | DownTime Category   | DownTime StartUTC    | DownTime EndUTC      | Created on local | Shift |
|---------|-----------------|--------------------|---------------------|----------------------|----------------------|------------------|-------|
| P129391 | BREADBLAST-OV04 | Water Supply Issue | Machine malfunction | 2/26/2023 4:38:13 PM | 2/26/2023 4:51:44 PM | 2/9/2023         | 1     |

The same mapping as in the last 2 examples.

> [!NOTE]
> The final map for column 2 will be:
>
> WorkCenter : Equipment.id

> [!NOTE]
> The final map for column 7 will be:
>
> Created on local : SegmentResponse.PostingDate

> [!NOTE]
> The final map for column 8 will be:
>
> Shift : SegmentData.Value

**Column 3: Reason description**

| ID      | WorkCenter      | Reason Description | DownTime Category   | DownTime StartUTC    | DownTime EndUTC      | Created on local | Shift |
|---------|-----------------|--------------------|---------------------|----------------------|----------------------|------------------|-------|
| P129391 | BREADBLAST-OV04 | Water Supply Issue | Machine malfunction | 2/26/2023 4:38:13 PM | 2/26/2023 4:51:44 PM | 2/9/2023         | 1     |

Using the same entity "Operations Event", we will find the attribute "Description".

> [!NOTE]
> The final map for column 3 will be:
>
> Reason description : OperationsEvent.Description

**Column 4: Downtime Category**

| ID      | WorkCenter      | Reason Description | DownTime Category   | DownTime StartUTC    | DownTime EndUTC      | Created on local | Shift |
|---------|-----------------|--------------------|---------------------|----------------------|----------------------|------------------|-------|
| P129391 | BREADBLAST-OV04 | Water Supply Issue | Machine malfunction | 2/26/2023 4:38:13 PM | 2/26/2023 4:51:44 PM | 2/9/2023         | 1     |

Using the same entity "Operations Event", we will find the attribute "Category".

> [!NOTE]
> The final map for column 4 will be:
>
> Downtime Category : OperationsEvent.Category

**Columns 5,6: DowntimeStartUTC, DowntimeEndUTC**

| ID      | WorkCenter      | Reason Description | DownTime Category   | DownTime StartUTC    | DownTime EndUTC      | Created on local | Shift |
|---------|-----------------|--------------------|---------------------|----------------------|----------------------|------------------|-------|
| P129391 | BREADBLAST-OV04 | Water Supply Issue | Machine malfunction | 2/26/2023 4:38:13 PM | 2/26/2023 4:51:44 PM | 2/9/2023         | 1     |

The start and end timestamps of an Operations Event is not an attribute of the "Operations Event" entity itself –or any other entity in the Operations Event model for the matter -, so we need to use a linked entity: "Operations event property" to map these timestamps after creating these start and end timestamps as "Operations Event Property".

![the image shows the relationship between Ops Event and Ops event property](../media/ops-event-ops-evet-property-relationship.png)

> [!NOTE]
> The final map for column 5 will be:
>
> DowntimeStartUTC : OperationsEventProperty.{id:"start-"ID}.value

> [!NOTE]
> The final map for column 6 will be:
>
> DownTimeEndUTC : OperationsEventProperty.{id:"end-"ID}.value
