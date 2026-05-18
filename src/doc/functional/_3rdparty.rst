
Authentic Sources Services
--------------------

Authentic Sources Services consist of a set of services implemented on top of identity systems to favour third parties
consumption of identity data. The services can be classified in four sets:

- Public discovery of available attributes and data services (Discover)

- Submitting citizen ID attributes for validation (Verify)

  The purpose is to extend the use of government-issued identity to registered
  third party services. The individual will submit their ID attributes to the third party in order to enroll
  for, or access, a particular service. The third party will leverage the API to access the identity
  management system and verify the individual's identity. In this way, external third parties can quickly and
  easily verify individuals based on their government issued ID attributes.

  .. admonition:: Use case applications: telco enrollment

      The API enables a telco operator to check an individual's identity when he is applying for a service contract.
      The telco relies on the government to confirm that the attributes submitted by the individual match against
      the data held in the database therefore being able to confidently identify the new subscriber. This scenario
      can be replicated across multiple sectors including banking and finance.

- Retrieve actual attribute values from the authentic source (Retrieve)

- Find persons by partial attribute matching when exact identifiers are unknown (Identify)

Note: The described Authentic Sources Services are compliant with the eIDAS regulation from a functional standpoint (Ref. ETSI 119 478).

Services
""""""""
.. py:function:: discover/internationalDiscover(country)
    :noindex:

    Return the uri of the discovery service for a specific country code and return the status of the discover interfaces and the country avaliable for this interface

    **Authorization**: none

    :param str country: country code of the targeted country (ISO 3166-1 alpha-2)
    :return: .. code-block:: json

   {
     "uri": "<string:uri>",
     "status" : "<string:enum(Up, Down)>",
      "supportedCountry" : ["<string:iso3166-alpha2>"]
   }
    
    In case of error the value is replaced with an error code

.. py:function:: discover/search(assetType, creator, country, text, semanticDataSpecification, schemaMediaType)
    :noindex:

    Searches the semantic repository for available attributes.

    **Authorization**: none

    :param str(fixed: `attribute`) assetType: Asset type to search for; must be `attribute`
    :param str creator: Filter results by attribute creator name
    :param str country: Filter results by country code (ISO 3166-1 alpha-2)
    :param str text: Free-text search across attribute metadata
    :param str semanticDataSpecification: filter by semantic data specification URI
    :param str schemaMediaType: Filter by schema media type
    :return: .. code-block:: json
{
  "attributes": [
    {
      "attributeIdentifier": "<string:uri>",
      "title": [
        { "value": "<string>", "language": "<string:iso639-1>" }
      ],
      "description": [
        { "value": "<string>", "language": "<string:iso639-1>" }
      ],
      "creator": "<string>",
      "country": "<string:iso3166-alpha2>",
      "semanticDataSpecification": "<string:uri>",
      "schemaDistribution": [
        { "accessURL": "<string:uri>", "mediaType": "<string>" }
      ]
    }
  ]
}
    In case of error the value is replaced with an error code

.. py:function:: discover/retrieve(queryType, attributeIdentifier, country)
    :noindex:

    Finds data service endpoints for specific attributes.

    **Authorization**: none

    :param str(fixed: `dataServices`) queryType: Query type; must be `dataServices`
    :param str:uri attributeIdentifier: Unique URI identifier for the attribute
    :param str country: Filter data services by country code (ISO 3166-1 alpha-2)
    :return: .. code-block:: json

   {
     "dataServices": [
       {
         "attributeIdentifier": "<string:uri>",
         "endpointDescription": "<string:uri>",
         "endpointURI": "<string:uri>",
         "provider": {
           "legalName": "<string>",
           "identifiers": [
             {
               "type": "<string:uri>",
               "identifier": "<string>"
             }
           ],
           "establishedByLaw": {
             "legislativeIdentifier": "<string:uri>",
             "legalBasis": "<string>"
           }
         },
         "country": "<string:iso3166-alpha2>"
       }
     ]
   }
    In case of error the value is replaced with an error

.. py:function:: verify/verify(attributes, attributeFragments, mandate)
    :noindex:

    Verify attributes against the authentic source without retrieving full data. Supports full attribute verification, fragment verification (privacy-preserving using JSONPath), or both.
    The required identification data of the user shall be contained in the access token provided by the Authorization Server.

    **Authorization**: `id.verify`

    :param array attributes: Complete attributes to verify (conditional: required if attributeFragments absent)
    :param array attributeFragments: Attribute fragments for privacy-preserving verification (conditional: required if attributes absent)
    :param object mandate: Mandate for delegated access on behalf of another data subject
    :return: .. code-block:: json

   {
     "responseId": "<string:uuid>",
     "provider": "<Provider>",
     "authenticSource": "<Provider>",
     "attributeVerificationResults": [
       {
         "attributeIdentifier": "<string:uri>",
         "attributeVerificationResult": "<string:uri:enum(Match|NoMatch|MatchWithVariation|Unknown)>",
         "VerificationResult": "<string:enum(Match|NoMatch|MatchWithVariation|Unknown)>",
         "attributeValue": "<object>"
       }
     ],
     "fragmentVerificationResults": [
       {
         "attributeIdentifier": "<string:uri>",
         "location": "<string:jsonpath>",
         "fragmentVerificationResult": "<string:uri:enum(Match|NoMatch|MatchWithVariation|Unknown)>",
         "VerificationResult": "<string:enum(Match|NoMatch|MatchWithVariation|Unknown)>",
         "fragmentValue": "<any>"
       }
     ],
     "mandateResult": "<MandateResult>"
   }
    In case of error the value is replaced with an error

.. py:function:: verify/verify/{deferredResponseId}(deferredResponseId)
    :noindex:

    Polls for deferred verification results.
    The required identification data of the user shall be contained in the access token provided by the Authorization Server.

    **Authorization**: `id.verify`

    :param str:uuid deferredResponseId: Identifier from the deferred response
    :return: Same as verify/verify response when ready, or HTTP 202 with DeferredResponse while pending
    In case of error the value is replaced with an error

.. py:function:: retrieve/retrieve(attributeIdentifiers, mandate)
    :noindex:

    Retrieve actual attribute values from the authentic source.
    The required identification data of the user shall be contained in the access token provided by the Authorization Server.

    **Authorization**: `id.retrieve`

    :param array:uri attributeIdentifiers: URIs of attributes to retrieve from the authentic source (min 1)
    :param object mandate: Mandate for delegated retrieval on behalf of another data subject
    :return: .. code-block:: json

   {
     "responseId": "<string:uuid>",
     "provider": "<Provider>",
     "authenticSource": "<Provider>",
     "attributes": [
       {
         "attributeIdentifier": "<string:uri>",
         "attributeValue": "<object>",
         "stringRetrieveResult": "<string:enum(Success|Failure)>"
       }
     ],
     "mandateResult": "<MandateResult>"
   }
    In case of error the value is replaced with an error

.. py:function:: retrieve/retrieve/{deferredResponseId}(deferredResponseId)
    :noindex:

    Polls for deferred retrieval results.
    The required identification data of the user shall be contained in the access token provided by the Authorization Server.

    **Authorization**: `id.retrieve`

    :param str:uuid deferredResponseId: Identifier from the deferred response
    :return: Same as retrieve/retrieve response when ready, or HTTP 202 with DeferredResponse while pending
    In case of error the value is replaced with an error

.. py:function:: retrieve/readAttributes(outputAttributeSet)
    :noindex:

    Get a list of identity attributes attached to a user.
    The required identification data of the user shall be contained in the access token provided by the Authorization Server.

    **Authorization**: `id.read`

    :param list[str] outputAttributeSet: defining the identity attributes to be provided back to the caller
    :return: An array of the requested attributes

    In case of error (unknown attributes, unauthorized access, etc.) the value is replaced with an error

.. py:function:: 'retrieve/readAttributeSet(Identifier, AttributeSetName)
    :noindex:

    Get a set of identity attributes as defined by attributeSet, attached to a user.
    The required identification data of the user shall be contained in the access token provided by the Authorization Server.

    **Authorization**: `id.set.read`

    :param str attributeSetName: The name of predefined attributes set name
    :return: An array of the requested attributes

    In case of error (unknown attributes, unauthorized access, etc.) the value is replaced with an error

.. py:function:: identify/identify(attributeSet, outputAttributeSet)
    :noindex:

    Identify possibly matching identities against an input set of attributes. Returns an array of predefined
    datasets as described by outputAttributeSet.
    The required identification data of the user shall be contained in the access token provided by the Authorization Server.

    Note: This service may be limited to some specific government RPs

    **Authorization**: `id.identify`

    :param list[str] attributeSet: A list of pair (name,value) requested
    :param list[str] outputAttributeSet: An array of attributes requested
    :return: Y or N
    
    In case of error (unknown attributes, unauthorized access, etc.) the value is replaced with an error


Attribute set
"""""""""""""

When identity attributes are exchanged, they are included in an attribute set, possibly containing groups like
biographic data, biometric data, document data, contact data... This structure is extensible and may be complemented
with other data groups, and each group may contain any number of attribute name / attribute value pairs.

Attribute set name
""""""""""""""""""

Attribute sets are by definition structures with variable and optional content, hence it may be useful to pre-agree
on a given attribute set content and name between two or more systems in a given project scope.

Any string may be used to define an attribute set name, but in the scope of this specification following names are
reserved and predefined:

.. list-table::
    :header-rows: 1

    * - Name
      - Description
      - Data Included
    * - "DEFAULT_SET_01"
      - Minimum demographic data
      - - First name
        - Last name
        - DoB
        - Place of birth
    * - "DEFAULT_SET_02"
      - Minimum demographic and portrait
      - Minimum demographic data + portrait
    * - "DEFAULT_SET_EIDAS"
      - Set expected to comply with eIDAS pivotal attributes [#]_.
      - Mandatory attributes:

        - First name
        - Last name
        - DoB
        - Identifier 

        Optional attributes:

        - Birth name 
        - Place of birth 
        - Gender
        - Current address



Output Attribute set
""""""""""""""""""""

To specify what identity attributes are expected in return when performing e.g. an identify request or a read attributes.

Data Model
""""""""""

.. list-table:: Authentic Source Data Model
    :header-rows: 1
    :widths: 25 50 25

    * - Type
      - Description
      - Example

    * - LocalizedText
      - A text value with its associated language code (ISO 639-1).
        Contains ``value`` (string) and ``language`` (string).
      - ``{"value": "Date of Birth", "language": "en"}``

    * - Provider
      - Legal entity operating as ASIP or DIP. Contains ``legalName`` (string),
        ``identifiers`` (array of ProviderIdentifier), ``establishedByLaw`` (EstablishedByLaw),
        and ``currentAddress`` (string).
      - ``{"legalName": "Ministry of Interior", "country": "FR"}``

    * - ProviderIdentifier
      - Typed identifier for a provider. Contains ``type`` (URI identifying the scheme)
        and ``identifier`` (string value within the scheme).
      - ``{"type": "urn:etsi:uri:vatin", "identifier": "FR12345678901"}``

    * - EstablishedByLaw
      - Legal basis for public sector entities. Contains ``legislativeIdentifier`` (URI)
        and ``legalBasis`` (human-readable description).
      - ``{"legalBasis": "Law 2021-123 on digital identity"}``

    * - VerificationAttribute
      - Complete attribute to verify against authentic source. Contains
        ``attributeIdentifier`` (URI) and ``attributeValue`` (JSON object).
      - ``{"attributeIdentifier": "urn:etsi:19478:attribute:naturalperson:DateOfBirth/v1.0", "attributeValue": {"date": "1990-05-15"}}``

    * - AttributeFragment
      - Privacy-preserving fragment for verification using JSONPath. Contains
        ``attributeIdentifier`` (URI), ``location`` (JSONPath expression), and ``value`` (any).
      - ``{"attributeIdentifier": "urn:...:DateOfBirth/v1.0", "location": "$.year", "value": 1990}``

    * - AttributeVerificationResult
      - Result for a verified attribute. Contains ``attributeIdentifier`` (URI),
        ``attributeVerificationResult`` (URI enum: Match, NoMatch, MatchWithVariation, Unknown),
        ``VerificationResult`` (string), and ``attributeValue`` (object, conditional).
      - ``{"attributeVerificationResult": "http://uri.etsi.org/19478/VerificationResult/Match"}``

    * - FragmentVerificationResult
      - Result for a verified fragment. Contains ``attributeIdentifier`` (URI),
        ``location`` (JSONPath), ``fragmentVerificationResult`` (URI enum),
        ``VerificationResult`` (string), and ``fragmentValue`` (any, conditional).
      - ``{"location": "$.year", "fragmentVerificationResult": ".../Match"}``

    * - SchemaDistribution
      - Location where an attribute schema can be accessed. Contains ``accessURL`` (URI)
        and ``mediaType`` (string).
      - ``{"accessURL": "https://example.eu/schemas/dob.json", "mediaType": "application/json"}``

    * - DataService
      - Data service endpoint for verification/retrieval. Contains ``attributeIdentifier`` (URI),
        ``endpointDescription`` (URI to API spec), ``endpointURI`` (base URL),
        ``provider`` (Provider), and ``country`` (ISO 3166-1 alpha-2).
      - ``{"endpointURI": "https://as.example.eu/api/v1"}``

    * - Mandate
      - Delegated authority for acting on behalf of another data subject. Contains
        ``mandateType`` (string), ``mandateReference`` (string), ``delegator`` (object),
        ``delegate`` (object), ``validFrom``, ``validUntil`` (date-time), and ``scope`` (array of URI).
      - ``{"mandateType": "LEGAL_REPRESENTATIVE", "mandateReference": "MND-2024-001"}``

    * - MandateResult
      - Outcome of mandate validation. Contains ``mandateValid`` (boolean),
        ``mandateReference`` (string), ``validationTime`` (date-time), ``delegatorMatch``,
        ``delegateMatch`` (boolean), and ``validationDetails`` (object).
      - ``{"mandateValid": true, "mandateReference": "MND-2024-001"}``




.. [#] `eIDAS SAML Attribute Profile <https://ec.europa.eu/cefdigital/wiki/download/attachments/82773108/eidas_saml_attribute_profile_v1.0_2.pdf?version=1&modificationDate=1497252920317&api=v2>`_

