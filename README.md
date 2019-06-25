# EducatorsHandbook.com Public API
The EducatorsHandbook.com Public API provides access to incident data in a machine-readable format.  It can be used by districts and third-party vendors to integrate EducatorsHandbook.com data with other applications.  The REST-style API is accessed via HTTPS using the GET method and returns XML-formatted responses.

## URI
https://api.educatorshandbook.com/public/v1/incidents.xml

## Authorization
All API requests must include an Authorization header containing a valid client access token in OAuth 2 Bearer Token format (i.e. `Authorization Bearer ClientAccessToken`).  Any request that does not contain a valid token will fail and return a `401 Unauthorized` response.

Client access tokens are managed via the API integration settings in your EducatorsHandbook.com account.  **API integration settings are hidden by default.  Open the settings view and press the `SHIFT` key twice to enable advanced settings.**  Anyone in possession of a valid client access token will have full access to the data in your account – they should be protected like a password.  We strongly recommend using a different token for each integration or script that will be making API requests.

## Optional Query Parameters
By default, the API will return all incidents from the current school term paginated to 1,000 members.  Use the following query parameters to alter this behavior.

##### `page={integer}`
Use the `page` parameter to request a specific page of results.  Page numbering is 1-based.  Omitting the `page` parameter will return the first page of results.  The current page and total number of pages are reported in the root node's `page` and `total-pages` attributes.

##### `type={referral|minor|pending}`
Use the `type` parameter to request incidents of a specific type.

##### `deleted=1`
Use the `deleted` parameter to include deleted incidents in the results.  Deleted incidents will have a `deleted` attribute set on the incident node.

##### `from={YYYY-MM-DD}`, `to={YYYY-MM-DD}`
Use both the `from` and `to` parameters to request results from a specific timeframe.

## Response Format
Members are returned inside of an `incidents` root node that has `count`, `page`, and `total-pages` attributes set to help you traverse the results.  Each incident is identified by a `uuid` value, which is unique across all EducatorsHandbook.com accounts.  Note that all nodes and attributes may not be present for all members.  For example, an incident without victims may not include the `victims` node.

```xml
<incidents count="{integer}" page="{integer}" total-pages="{integer}">
  <incident uuid="{string}" type="{string}" last-modified="{datetime}" deleted="{integer}">
    <date>{date}</date>
    <time>{time}</time>
    <written-by datetime="{datetime}" district-id="{string}">{string}</written-by>
    <school code="{string}">{string}</school>
    <location code="{string}">{string}</location>
    <subject code="{string}">{string}</subject>
    <arrangement code="{string}">{string}</arrangement>
    <offense code="{string}">{string}</offense>
    <description>{string}</description>
    <offenders count="{integer}">
      <offender>
        <student district-id="{string}">
          <name>{string}</name>
          <grade>{string}</grade>
          <gender>{string}</gender>
          <race>{string}</race>
          <ethnicity>{string}</ethnicity>
          <iep>{boolean}</iep>
          <plan>{boolean}</plan>
          <ell>{boolean}</ell>
        </student>
        <offenses count="{integer}">
          <offense code="{string}">{string}</offense>
          <!-- ... -->
        </offenses>
        <weapons count="{integer}">
          <weapon code="{string}">{string}</weapon>
          <!-- ... -->
        </weapons>
        <injury code="{string}">{string}</injury>
        <actions count="{integer}">
          <action code="{string}" cost="{boolean}">
            <name>{string}</name>
            <duration day-length="{integer}">{float}</duration>
            <schedule count="{integer}">
              <event>
                <date>{date}</date>
                <start>{time}</start>
                <end>{time}</end>
                <served>{boolean}</served>
              </event>
              <!-- ... -->
            </schedule>
            <details>{string}</details>
            <assigned-by datetime="{datetime}" district-id="{string}">{string}</assigned-by>
          </action>
          <!-- ... -->
        </actions>
      </offender>
      <!-- ... -->
    </offenders>
    <victims count="{integer}">
      <victim>
        <student district-id="{string}">
          <name>{string}</name>
          <grade>{string}</grade>
          <gender>{string}</gender>
          <race>{string}</race>
          <ethnicity>{string}</ethnicity>
          <iep>{boolean}</iep>
          <plan>{boolean}</plan>
          <ell>{boolean}</ell>
        </student>
      </victim>
      <!-- ... -->
    </victims>
  </incident>
  <!-- ... -->
</incidents>
```

## Examples

### cURL

```bash
curl "https://api.educatorshandbook.com/public/v1/incidents.xml" -H "Authorization: Bearer ClientAccessToken"
```

### PHP

```php
<?php
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://api.educatorshandbook.com/public/v1/incidents.xml');
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'GET');
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_HTTPHEADER, array(‘Authorization: Bearer ClientAccessToken’));

$resp = curl_exec($ch);
if (!$resp) {
  die('Error: "' . curl_error($ch) . '" - Code: ' . curl_errno($ch));
} else {
  echo "Response HTTP Status Code : " . curl_getinfo($ch, CURLINFO_HTTP_CODE);
  echo "\nResponse HTTP Body : " . $resp;
}

curl_close($ch);
?>
```
