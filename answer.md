"""
def ensure_request_is_valid(
    url,
    content_type,
    method,
    connection,
    requested_pot,
    accepted_coffee_schemes,
    accepted_methods,
    not_found_message,
):
    # TODO: Basic request checking
    """
    This method checks if the URL scheme is correct. You shall:

    1. Validate the scheme against accepted_coffee_schemes
    2. Check for correct URL path format <SCHEME>://<HOSTNAME>
    3. Validate the HTTP method: check method against accepted_methods
    4. Check the content type format to conform to "application/coffee-pot-command"
    5. Specific check for "tea" pot request

    If all checks pass, return True, otherwise return False

    For each case 1 to 5 above, call send_error_message(error_message) with an appropriately crafted error message containing status code and reason-phrase. The arg not_found_message gives you a general idea of the format of the expected error message conforming to HTCPCP/1.0 protocol.
    """

    # 1. Validate the scheme against accepted_coffee_schemes
    try:
        schema = url.split("://")[0]
    except Exception:
        schema = ""
    if schema not in accepted_coffee_schemes:
        error_message = "HTCPCP/1.1 400 Bad Request\r\n\r\nInvalid scheme"
        return send_error_message(connection, error_message.encode("utf-8"))

    # 2. Check for correct URL path format <SCHEME>://<HOSTNAME>
    try:
        hostname = url.split("://")[1]
    except Exception:
        hostname = ""
    if hostname != HOSTNAME:
        error_message = "HTCPCP/1.1 404 Not Found\r\n\r\nInvalid hostname"
        return send_error_message(connection, error_message.encode("utf-8"))

    # 3. Validate the HTTP method: check method against accepted_methods
    if method not in accepted_methods:
        error_message = "HTCPCP/1.1 501 Not Implemented\r\n\r\nInvalid method"
        return send_error_message(connection, error_message.encode("utf-8"))

    # 4. Check the content type format to conform to "application/coffee-pot-command"
    if len(content_type) == 0 or content_type[0] != "Content-Type: application/coffee-pot-command":
        error_message = "HTCPCP/1.1 415 Unsupported Media Type\r\n\r\nInvalid content type"
        return send_error_message(connection, error_message.encode("utf-8"))

    # 5. Specific check for "tea" pot request
    if requested_pot and "tea" in requested_pot.lower():
        error_message = "HTCPCP/1.1 418 I'm a teapot\r\n\r\nThis pot only brews coffee"
        return send_error_message(connection, error_message.encode("utf-8"))

    return True