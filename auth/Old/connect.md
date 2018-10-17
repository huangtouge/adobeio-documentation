# JWT Authentication Reference
To establish a secure service-to-service Adobe I/O API session, you must create a JSON Web Token (JWT) that encapsulates the identity of your integration, and exchange it for an access token. Every request to an Adobe service must include the access token in the **Authorization** header, along with the API Key (Client ID) that was generated when you created the integration in the [Adobe I/O Console](https://console.adobe.io/).

A typical access token is valid for 24 hours after it is issued.
You can request multiple access tokens. Previous tokens are not invalidated when a new one is issued. You can authorize requests with any valid access token. This allows you to overlap access tokens to ensure your integration is always able to connect to Adobe.
Access request syntax
To initiate an API session,use the JWT to obtain an access token from Adobe by making a POST request to Adobe's Identity Management Service (IMS).

Send a POST request to:

`https://ims-na1.adobelogin.com/ims/exchange/jwt`

The body of the request should contain URL-encoded parameters with your Client ID (API Key), Client Secret, and JWT:

`client_id={api_key_value}&client_secret={client_secret_value}&jwt_token={base64_encoded_JWT}`

## Request parameters
Pass URL-encoded parameters in the body of your POST request:

| Parameter | Description |
|---|---|
| **`client_id`**	| The API key generated for your integration. |
| **`client_secret`**	| The client secret generated for your integration. |
| **`jwt_token`**	| A base-64 encoded JSON Web Token that encapsulates the identity of your integration, signed with a private key that corresponds to a public key certificate attached to the integration. |

## Responses
When a request has been understood and at least partially completed, it returns with HTTP status 200. On success, the response contains a valid access token. Pass this token in the **Authorization header** in all subsequent requests to an Adobe service.

A failed request can result in a response with an HTTP status of 400 or 401 and one of the following error messages in the response body:

<table>
	<tbody>
		<tr>
			<td>
				<strong>400 invalid_client</strong>
			</td>
			<td>
				<ul>
					<li>Integration does not exist. This applies both to the <strong>client_id</strong> parameter and the <strong>aud</strong> in the JWT.</li>
					<li>The <strong>client_id</strong> parameter and the <strong>aud</strong> field in the JWT do not match.</li>
				</ul>
			</td>
		</tr>
		<tr>
			<td>
				<strong>401 invalid_client</strong>
			</td>
			<td>
				<ul>
					<li>Integration does not have the <strong>exchange_jwt</strong> scope. This indicates an improper client configuration. Contact Adobe I/O team to resolve it.</li>
					<li>The client ID and client secret combination is invalid.</li>
				</ul>
			</td>
		</tr>
		<tr>
			<td>
				<strong>400 invalid_token</strong>
			</td>
			<td>
				<ul>
					<li>JWT is missing or cannot be decoded.</li>
					<li>JWT has expired. In this case, the <strong>error_description</strong> contains more details.</li>
					<li>The <strong>exp</strong> or <strong>jti</strong> field of the JWT is not an integer.</li>
				</ul>
			</td>
		</tr>
		<tr>
			<td>
				<strong>400 invalid_signature</strong>
			</td>
			<td>
				<ul>
					<li>The JWT signature does not match any certificates attached to the integration.</li>
					<li>The signature does not match the algorithm specified in the JWT header.</li>
				</ul>
			</td>
		</tr>
		<tr>
			<td>
				<strong>400 invalid_jti</strong>
			</td>
			<td>
				<p>The binding requires a JTI, but the <strong>jti</strong> field is missing or was previously used.</p>
			</td>
		</tr>
		<tr>
			<td>
				<strong>400 invalid_scope</strong>
			</td>
			<td>
				<p>Indicates a problem with the requested scope for the token. The JWT must include <strong>"https://ims-na1.adobelogin.com/s/ent_user_sdk": true</strong>. Specific scope problems can be:</p>
				<ul>
					<li>Metascopes in the JWT do not match metascopes in the binding.</li>
					<li>Metascopes in the JWT do not match target client scopes</li>
					<li>Metascopes in the JWT contain a scope or scopes that do not exist.</li>
					<li>The JWT has no metascopes.</li>
				</ul>
			</td>
		</tr>
		<tr>
			<td>
				<strong>400 bad_request</strong>
			</td>
			<td>
				<p>JWT payload can be decoded and decrypted but contents are incorrect. Can occur when values for fields such as <strong>sub</strong>, <strong>iss</strong>, <strong>exp</strong>, or <strong>jti</strong> are not in the proper format.</p>
			</td>
		</tr>
	</tbody>
</table>

## Example

```html
========================= REQUEST ==========================
POST https://ims-na1.adobelogin.com/ims/exchange/jwt
-------------------------- body ----------------------------
client_id={myClientId}&client_secret={myClientSecret}&jwt_token={myJSONWebToken}
------------------------- headers --------------------------
Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
```

For an example of a Python script that creates and sends this type of request, see the [User Management Walkthrough](https://www.adobe.io/apis/cloudplatform/usermanagement/docs/samples.html).