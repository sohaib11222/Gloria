


=============================================
OPENTRAVEL 2024A README
=============================================

=============================
2024A Publication Contents
=============================

- The following documents are included with the OpenTravel 2021A Publication:

----------- OpenTravel_CodeTable (folder) -----------  
- OpenTravel_CodeLists_2024_12_10 - A folder containing the OpenTravel Code Table zip file in Microsoft Excel format. The OpenTravel Code List and the OpenTravel Optimized Code List (Phase I) spreadsheets, included with the specification download, includes an alphabetized, point and click list of all OpenTravel Code List names (with 3 character abbreviations) and errors and warnings to help implementers quickly find code list values.

----------- OpenTravel_2024A_XML (folder) -----------
- XML Schema files- OpenTravel XML Schema messages. 

----------- OpenTravel_2024A_Schema_Versioning.pdf ----------- 
- OpenTravel began tracking versions in the 2003B release cycle. Prior to the 2008A release, the version table contained 2001 and 2002 data for newly created messages. This document contains a table with each OpenTravel message version from 2006A through 2024A.

----------- OpenTravel_SchemaDesignBestPracticesV3.08.pdf ----------- 
- The OpenTravel XML Schema Best Practices document contains documentation about the standards and best practices used in creating the XML Schemas. Typically, this document is used within the OpenTravel Alliance to ensure that the creation of XML Schemas is consistent in design across the organization and across releases.

----------- OpenTravel_2024A_XMLMessageSuite_ReleaseNotes.pdf ----------- 
- The release notes detail the latest information and changes for any given release. This file contains detailed documentation on changes made between the 2021A release and the 2024A release. 

----------- OpenTravel_2024A_Message_Users_Guide.pdf ----------- 
- The Message Users Guide (MUG) contains a description of each OpenTravel Message.  It provides a high-level overview of message functionality. 

----------- OpenTravel_ImplementationGuide_v1.2_ExecSum.pdf -------------
- This is an executive summary of the OpenTravel Implementation Guide. The purpose of the OpenTravel Implementation Guide is to provide invaluable information to both analysts and implementers of the OpenTravel specification on how to more easily understand and build software systems that are interoperable with other travel systems using the OpenTravel schema. The full Guide is available to members of the OpenTravel Alliance via the Wiki http://wiki.opentravel.org/index.php/Public:Resources.

----------- OpenTravel_FastRez (folder) -----------
- OpenTravel_FastRez.zip: A zipped file containing OpenTravel FastRez Schema, WSDL and User Documentation.

----------- OpenTravel_Other (folder) -----------
- OpenTravel Hotel Schema Disability Audit Results: An OpenTravel Hotel Disability Project Brief artifact has been included with the OpenTravel 2011B 1.0 XML Schema Message publication. The OpenTravel Hotel Disability project was focused on a subset of the new 2010 ADA legislation that specifically applies to electronic hotel reservations and includes identifying and describing accessible features; reserving, upon request, accessible guest rooms or specific types of guest rooms and ensuring that the guest rooms requested are blocked and removed from all reservations systems; and guaranteeing the specific accessible guest room. Per the audit results, relatively few changes were made to OpenTravel Hotel schema, with one change made to one common hotel schema (OTA_HotelCommonTypes) to provide a more consistent and structured way to pass measurement information, and annotation changes made to three hotel schema (OTA_HotelCommonTypes, OTA_RailCommonTypes and OTA_RailPreferences) to accommodate renaming the OpenTravel Code List Physically Challenged Feature code table. The slide show included with the publication includes an overview of the project.

=================================================
2024A Significant Schema Updates
=================================================

----Modified Schema----

--The following fields have been added to support the Optimizing Lodging Content for the Distribution Ecosystem HTNG Workgroup, which included participants from HTNG, OpenTravel, and HEDNA.  This workgroup set out to update the OpenTravel 1.0 Messages to share current hotel content needs across the distribution ecosystem.  Below, you will find a list of changes incorporated into the messages.  
Common

•	Added element AlternateGeolocations, recurrence 0..1 to OTA_CommonTypes/RelativePosition/AlternateGeolocations.
•	Added element AlternateGeolocation, reccurence 1..n to OTA_CommonTypes/RelativePosition/AlternateGeolocations/AlternateGeolocation.
•	Added attribute Name of type StringLength1to128, required to OTA_CommonTypes/RelativePosition/AlternateGeolocations/AlternateGeolocation/Name.
•	Added attribute PositionCode of type StringLength1to255, required to OTA_CommonTypes/RelativePosition/AlternateGeolocations/AlternateGeolocation/PositionCode.
•	Added attribute URL of type AnyURI, optional to OTA_CommonTypes/RelativePosition/AlternateGeolocations/AlternateGeolocation/URL.
•	Added attribute DimensionCategory of type StringLength1to16, optional to OTA_CommonTypes/VideoDescriptionType/VideoFormat/@DimensionCategory.
•	Added attribute Sort of type PositiveInteger, optional to OTA_CommonTypes/VideoDescriptionType/VideoFormat/@Sort.
•	Added element URL_Caption of type StringLength1to128, recurrence 0..1 to OTA_CommonTypes/VideoItemType/URL_Caption
•	Added complex Type DressCodeType to OTA_CommonTypes/DressCodeType.
•	Added element DressCode, recurrence 1..n to OTA_CommonTypes/DressCodeType/DressCode.
•	Added attribute Code of type OTA_CodeType, optional to OTA_CommonTypes/DressCodeType//DressCode/@Code.
•	Added attribute group CodeInfoGroup to OTA_CommonTypes/DressCodeType//DressCode/ @group CodeInfoGroup.
•	Added element DressCodes of type DressCodeType, recurrence 0..1 to OTA_CommonTypes/RestaurantType/DressCodes.
•	Added attribute WhenCollected of type xs:string with enumerations “At time of booking” and “On property”, optional to OTA_CommonTypes/TaxType@WhenCollected.
•	Added attribute WhenCollected of type xs:string with enumerations “At time of booking” and “On property”, optional to OTA_CommonTypes/FeeType@WhenCollected.
•	Added attribute AllInAmount of type Money, optional to OTA_CommonTypes/TotalType/@AllInAmount.


HotelCommon

•	Added attribute GeothermalInd of type Boolean, optional to OTA_ HotelCommonTypes/EnvironmentalImpactType/Energy/@GeothermalInd.
•	Added attribute HydroEnergyInd of type Boolean, optional to HotelCommonTypes/EnvironmentalImpactType/Energy/@HydroEnergyInd.
•	Added attribute WasteToEnergyInd of type Boolean, optional to HotelCommonTypes/EnvironmentalImpactType/Energy/@WasteToEnergyInd.
•	Added attribute Percentage of type Percentage, optional to HotelCommonTypes/EnvironmentalImpactType/Energy/@WasteToEnergyInd.
•	Added attribute WasteToEnergyGenerationInd type Boolean, optional to HotelCommonTypes/EnvironmentalImpactType/Recycling/@WasteToEnergyGenerationInd.
•	Added attribute CompostingInd of type Boolean, optional to HotelCommonTypes/EnvironmentalImpactType/Recycling/Recycling/@CompostingInd.
•	Added attribute HotelContact of type StringLength1to255, optional to HotelCommonTypes/EnvironmentalImpact/TypeGeneral/@HotelContact.
•	Updated annotation on attribute MeetingEvent at HotelCommonTypes/EnvironmentalImpactType/CarbonFootprint/@MeetingEvent to “Meeting/ event carbon dioxide measurement. The CarbonUOM field is used to identify the unit of measure for this measurement (e.g. pounds, kilograms. etc.).”
•	Updated annotation on attribute RoomNight at HotelCommonTypes/EnvironmentalImpactType/CarbonFootprint/@RoomNight to “Room night carbon dioxide measurement. The CarbonUOM field is used to identify the unit of measure for this measurement (e.g. pounds, kilograms. etc.).”
•	Added attribute Rating of type StringLength1to32, optional to HotelCommonTypes/EnvironmentalImpactType/General/Certification/@Rating.
•	Added attribute RatingSymbol of type StringLength1to31, optional to HotelCommonTypes/EnvironmentalImpactType/General/Certification/@RatingSymbol.
•	Added element WasteFootprint, recurrence 0..1 to HotelCommonTypes/EnvironmentalImpactType/WasteFootprint.
•	Added attribute TotalWaste of type PositiveInteger, optional to HotelCommonTypes/EnvironmentalImpactType/WasteFootprint/@TotalWaste.
•	Added attribute WasteDiversionRate of type Percentage, optional to HotelCommonTypes/EnvironmentalImpactType/WasteFootprint/@WasteDiversionRate.
•	Added attribute WasteUOM of type List_UnitOfMeasure, optional, to HotelCommonTypes/EnvironmentalImpactType/WasteFootprint/WasteUOM.
•	Added attribute LoyaltyPointEligibleInd of type xs:Boolean, optional, to OTA_HotelCommonTypes/RatePlanType/@LoyaltyPointEligibleInd.


HotelContentDescription

•	Added element IDs, recurrence 0..1 to OTA_HotelContentDescription/HotelDescriptiveContentType/IDs.
•	Added element ID, recurrence 1..n to OTA_HotelContentDescription/HotelDescriptiveContentType/IDs/ID.
•	Added attribute ID of type StringLength1to32, optional to OTA_HotelContentDescription/HotelDescriptiveContentType/IDs/ID/@ID.
•	Added attribute ID_Description of type StringLength0to128, optional to OTA_HotelContentDescription/HotelDescriptiveContentType/IDs/ID/@ID_Description.
•	Added element GuestReviews, recurrence 0..1 to OTA_HotelContentDescription/HotelDescriptiveContentType/GuestReviews.
•	Added element GuestReview, reccurence 1..n to OTA_HotelContentDescription/HotelDescriptiveContentType/GuestReviews/GuestReview.
•	Added element Ratings, recurrence 0..1 to OTA_HotelContentDescription/HotelDescriptiveContentType/GuestReviews/GuestReview/Ratings.
•	Added attribute RecommendationPercent of type Percentage, optional to OTA_HotelContentDescription/HotelDescriptiveContentType/GuestReviews/GuestReview/Ratings/@RecommendationPercent.
•	Added attribute DataSource of type StringLength0to128, optional to OTA_HotelContentDescription/HotelDescriptiveContentType/GuestReviews/GuestReview/Ratings/@DataSource.
•	Added element Rating, recurrence 0..n to OTA_HotelContentDescription/HotelDescriptiveContentTypeGuestReviews/GuestReview/Ratings/Rating.
•	Added attribute GuestReviewRatingCategoryCode of type OTA_CodeType, optional to OTA_HotelContentDescription/HotelDescriptiveContentType/GuestReviews/GuestReview/Ratings/Rating/@GuestRatingCategoryCode.
•	Added attribute Rating of Type Decimal, optional, to OTA_HotelContentDescription/HotelDescriptiveContentType/GuestReviews/GuestReview/Ratings/Rating/@Rating.
•	Added attribute MaximumRating of type Integer, optional to OTA_HotelContentDescription/HotelDescriptiveContentType/GuestReviews/GuestReview/Ratings/Rating/@MaximumRating.
•	Added attribute ReviewCount of type Integer, optional to OTA_HotelContentDescription/HotelDescriptiveContentType/GuestReviews/GuestReview/Ratings/Rating/@ReviewCount.
•	Added element ReviewTextHighlights, recurrence 0..1 to OTA_HotelContentDescription/HotelDescriptiveContentType/GuestReviews/GuestReview/Ratings/Rating/ReviewTextSnippets.
•	Added element ReviewTextHighlight of type ParagraphType, recurrence 0..n to OTA_HotelContentDescription/HotelDescriptiveContentType/GuestReviews/GuestReview/Ratings/Rating/ReviewTextSnippets/ReviewTextSnippet.
•	Added element UnitConfiguration, recurrence 0..n to OTA_HotelContentDescription/HotelDescriptiveContentType/FacilityInfo/GuestRooms/GuestRoom/UnitConfiguration.
•	Added attribute TypeOfRoom of type OTA_CodeType, optional to OTA_HotelContentDescription/HotelDescriptiveContentType/FacilityInfo/GuestRooms/GuestRoom/Configuration/@TypeOfRoom.
•	Added attribute Quantity of type Integer, optional to OTA_HotelContentDescription/HotelDescriptiveContentType/FacilityInfo/GuestRooms/GuestRoom/Configuration/@Quantity.
•	Added element FAQs, recurrence 0..1 to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs.
•	Added element FAQ, recurrence 1..n to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ.
•	Added attribute locale of type StringLength0to8, optional to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/@Locale.
•	Added element FAQ_Details recurrence 1, to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/FAQ_Details.
•	Added element FAQ_Detail reccurence 0..n to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/FAQ_Details/FAQ_Detail.
•	Added attribute SortOrder of type PositiveInteger, optional to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/FAQ_Details/FAQ_Detail/@SortOrder.
•	Added attribute Category of type StringLength0to128, optional to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/FAQ_Details/FAQ_Detail/@Category.
•	Added attribute Removal of type Boolean, optional to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/FAQ_Details/FAQ_Detail/@Removal.
•	Added element Question of type String, recurrence 1 to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/FAQ_Details/FAQ_Detail/Question.
•	Added element Answer of type String, recurrence 1 to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/FAQ_Details/FAQ_Detail/Answer.
•	Added element DisplayDateRanges, recurrence 0..1  to OTA_HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/FAQ_Details/FAQ_Detail/DisplayDateRanges.
•	Added element DisplayDateRange, recurrence 1..n to OTA_HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/FAQ_Details/FAQ_Detail/DisplayDateRanges/DisplayDateRange.
•	Added attribute Start of type DateOrTimeOrDateTimeType, optional to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/FAQ_Details/FAQ_Detail/DisplayDateRanges/DisplayDateRange/@Start.
•	Added attribute Duration of type DurationType, optional to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/FAQ_Details/FAQ_Detail/DisplayDateRanges/DisplayDateRange/@Duration.
•	Added attribute End of type DateOrTimeOrDateTimeType, optional to OTA_HotelDescriptiveContentNotifRQ/HotelDescriptiveContents/HotelDescriptiveContent/FAQs/FAQ/FAQ_Details/FAQ_Detail/DisplayDateRanges/DisplayDateRange/@End.
•	Added element RatePlans, recurrence 0..1 to OTA_HotelDescriptiveContent/RatePlans.
•	Added element RatePlan, recurrence 1..n to OTA_HotelDescriptiveContent/RatePlans/RatePlan.
•	Added attribute RatePlanName of type StringLength0to128, optional to OTA_HotelDescriptiveContent/RatePlans/RatePlan/@RatePlanName.
•	Added attribute RatePlanCode of type StringLength0to8, optional to OTA_HotelDescriptiveContent/RatePlans/RatePlan/@RatePlanCode.
•	Added element Descriptions, recurrence 0..1 to OTA_HotelDescriptiveContent/RatePlans/RatePlan/Descriptions.
•	Added element Description, recurrence 1..n to OTA_HotelDescriptiveContent/RatePlans/RatePlan/Descriptions/Description.
•	Added element FeesSchedules, recurrence 0..1 to OTA_HotelContentDescription/PoliciesType/Policy/PetsPolicies/PetsPolicy/FeeSchedules.
•	Added element FeeSchedule, recurrence 1..n to OTA_HotelContentDescription/PoliciesType/Policy/PetsPolicies/PetsPolicy/FeeSchedules/FeeSchedule.
•	Added attribute Refundable of type Boolean, optional to OTA_HotelContentDescription/PoliciesType/Policy/PetsPolicies/PetsPolicy/FeeSchedules/FeeSchedule/@Refundable.
•	Added attribute PetQuantity of type NonNegativeInteger, optional to OTA_HotelContentDescription/PoliciesType/Policy/PetsPolicies/PetsPolicy/FeeSchedules/FeeSchedule/@PetQuantity.
•	Added attribute MinNumberOfDays of type Integer, optional to OTA_HotelContentDescription/PoliciesType/Policy/PetsPolicies/PetsPolicy/FeeSchedules/FeeSchedule/@MinNumberofDays.
•	Added attribute MaxNumberOfDays of type Integer, optional to OTA_HotelContentDescription/PoliciesType/Policy/PetsPolicies/PetsPolicy/FeeSchedules/FeeSchedule/@MaxNumberofDays.
•	Added attribute ChargeCode of type OTA_CodeType, optional to OTA_HotelContentDescription/PoliciesType/Policy/PetsPolicies/PetsPolicy/FeeSchedules/FeeSchedule/@ChargeCode.
•	Added attribute group CurrencyAmountGroup to OTA_HotelContentDescription/PoliciesType/Policy/PetsPolicies/PetsPolicy/FeeSchedules/FeeSchedule/@group CurrencyAmountGroup.
•	Added element DressCodes of type DressCodeType recurrence 0..1 to OTA_HotelContentDescription/AreaInfoType/Recreations/Recreation/DressCodes.
•	Added element DressCodes of type DressCodeType recurrence 0..1 to OTA_HotelContentDescription/AreaInfoType/Recreations/Recreation/RecreationDetails/RecreationDetail/DressCodes.

HotelRatePlan

•	Added attribute LoyaltyPointEligibleInd of type xs:Boolean, optional, to OTA_HotelCommonTypes/HotelRatePlanType/@LoyaltyPointEligibleInd.


======================================================
OpenTravel Implementation Guide Available for Members
======================================================

The OpenTravel Implementation Guide provides information that an implementer of the OpenTravel specification can use to more easily build software systems that are interoperable with other travel systems. The guide also should be useful for analysts who need to understand how to use the OpenTravel specification. The OpenTravel Implementation Guide is available to members through the OpenTravel wiki http://wiki.opentravel.org/index.php/Members:OpenTravel_Implementation_Guide.

======================================
Other Resources
======================================

Comments may be submitted at any time via the comment submission form located at http://www.opentravel.org/Specifications/CommentOnSpec.aspx  

- The OpenTravel Forum http://www.OpenTravelForum.com
OpenTravel has an extensive discussion Forum to provide an implementation resource for users of its schema, called the OpenTravel Forum, which has all the functionality members expect from a full-featured discussion board, with forums for Architecture, Hospitality, Transport, Travel Services, Tours and Activities, and Implementers Discussion. Also included are OpenTravel documentation, mailing list subscription, events and announcements, and feedback boards, as well as the OpenTravel Showcase where companies that provide tools, services or technologies to assist in the implementation of OpenTravel schemas can post about their offerings.

- OpenTravel Message Codes List Table
The OpenTravel Codes List spreadsheet, included with the specification download, includes a worksheet named “Index.” This index contains a new, alphabetized, point and click list of all OpenTravel Code List names and 3 character abbreviations to help implementers quickly find code list values.



