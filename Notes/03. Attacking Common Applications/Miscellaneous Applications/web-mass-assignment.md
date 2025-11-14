# Web Mass Assignment Vulnerabilities

## 1. Concept

A **mass assignment vulnerability** occurs when an application automatically maps user-supplied data to model attributes without filtering or validation.
Attackers can exploit this by modifying HTTP parameters to change protected fields — for example, setting `admin=true` or bypassing account approvals.

**Common impacts:**

* Unauthorized data modification
* Privilege escalation
* Bypassing approval or verification processes

---

## 2. Exploitation Examples

### Python / SQLite Example

**Vulnerable Code:**

```python
try:
  if request.form['confirmed']:
    cond = True
except:
  cond = False

with sqlite3.connect("database.db") as con:
  cur = con.cursor()
  cur.execute('select * from users where username=?', (username,))
  if cur.fetchone():
    return render_template('index.html', value='User exists!!')
  else:
    cur.execute('insert into users values(?,?,?)', (username, password, cond))
    con.commit()
    return render_template('index.html', value='Success!!')
```

**Exploit Example:**

```http
POST /register HTTP/1.1
Host: <target>
Content-Type: application/x-www-form-urlencoded

username=newuser&password=password123&confirmed=1
```

The attacker can set `confirmed` manually, gaining access or bypassing validation.

---

### Ruby on Rails

**Vulnerable Code:**

```ruby
class UsersController < ApplicationController
  def create
    @user = User.new(params[:user]) # Vulnerable mass assignment
    if @user.save
      redirect_to @user
    else
      render 'new'
    end
  end
end
```

An attacker could send an HTTP request with `admin=true`, escalating privileges.

**Fix (Strong Parameters):**

```ruby
class UsersController < ApplicationController
  def create
    @user = User.new(user_params)
    if @user.save
      redirect_to @user
    else
      render 'new'
    end
  end

  private
  def user_params
    params.require(:user).permit(:username, :email) # Only allow specific fields
  end
end
```

---

### Laravel (PHP)

**Vulnerable Code:**

```php
class User extends Model {
    protected $guarded = []; // All fields assignable (unsafe)
}
```

**Fix:**

```php
class User extends Model {
    protected $fillable = ['username', 'email']; // Whitelist only safe attributes
}
```

---

### Express (Node.js)

**Vulnerable Code:**

```javascript
app.post('/register', async (req, res) => {
    const user = await User.create(req.body);
    res.send(user);
});
```

**Fix:**

```javascript
app.post('/register', async (req, res) => {
    const user = await User.create({
        username: req.body.username,
        email: req.body.email
    });
    res.send(user);
});
```

---

## 3. Prevention Techniques

* **Whitelisting:** Explicitly allow safe parameters only.
* **Use ORM Security Features:**
  * Rails - `strong_parameters`
  * Laravel - `$fillable`
  * Sequelize - define strict schemas
* **Validate Input:** Reject unrecognized fields and enforce type checks.
* **Apply Least Privilege:** Restrict which users can alter which fields.
* **Database Constraints:** Use `NOT NULL`, `CHECK`, and restricted enums.
* **Monitor & Log:** Detect abnormal field modification attempts.
* **Regular Code Reviews:** Catch unsafe model bindings early.

---

## 4. Real-World Exploits

* **GitHub (2012):** Attackers changed repository collaborator roles via mass assignment, gaining admin access.
* **Shopify:** A mass assignment bug allowed privilege escalation in the store admin panel.
