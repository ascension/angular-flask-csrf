# angular-flask-csrf


def generate_csrf_token():
    if 'XSRF-TOKEN' in request.cookies:
        return request.cookies.get('XSRF-TOKEN')
    else:
        import string, random
        return "".join(
            [random.choice(string.ascii_letters + string.digits)
                for n in xrange(30)])

@app.before_request
def csrf_protect():
    if request.method == "POST":
        # Compare header token to cookie token that only our domain can read
        if request.headers['X-XSRF-TOKEN'] != request.cookies.get('XSRF-TOKEN'):
            abort(403)
    elif request.method == 'GET':
        # If XSRF token is not set in cookie, set it
        if 'XSRF-TOKEN' not in request.cookies:
            @after_this_request
            def set_csrf_cookie(response):
                response.set_cookie('XSRF-TOKEN', generate_csrf_token())
