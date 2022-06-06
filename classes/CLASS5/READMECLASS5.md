### Advanced M:N Associations

###### Using One-to-Many relationships instead
- Instead of setting up the Many-to-Many relationship defined above, what if we did the following instead?

```javascript
// Setup a One-to-Many relationship between User and Grant
User.hasMany(Grant);
Grant.belongsTo(User);

// Also setup a One-to-Many relationship between Profile and Grant
Profile.hasMany(Grant);
Grant.belongsTo(Profile);

```

###### The best of both worlds: the Super Many-to-Many relationship
- We can simply combine both approaches shown above!

```javascript
// The Super Many-to-Many relationship
User.belongsToMany(Profile, { through: Grant });
Profile.belongsToMany(User, { through: Grant });
User.hasMany(Grant);
Grant.belongsTo(User);
Profile.hasMany(Grant);
Grant.belongsTo(Profile);

```

###### Aliases and custom key names
Similarly to the other relationships, aliases can be defined for Many-to-Many relationships.

Before proceeding, please recall the aliasing example for belongsTo on the associations guide. Note that, in that case, defining an association impacts both the way includes are done (i.e. passing the association name) and the name Sequelize chooses for the foreign key (in that example, leaderId was created on the Ship model).

Defining an alias for a belongsToMany association also impacts the way includes are performed:

```javascript
Product.belongsToMany(Category, { as: 'groups', through: 'product_categories' });
Category.belongsToMany(Product, { as: 'items', through: 'product_categories' });

// [...]

await Product.findAll({ include: Category }); // This doesn't work

await Product.findAll({ // This works, passing the alias
  include: {
    model: Category,

```



##### Specifying attributes from the through table
By default, when eager loading a many-to-many relationship, Sequelize will return data in the following structure (based on the first example in this guide):

```javascript
// User.findOne({ include: Profile })
{
  "id": 4,
  "username": "p4dm3",
  "points": 1000,
  "profiles": [
    {
      "id": 6,
      "name": "queen",
      "grant": {
        "userId": 4,
        "profileId": 6,
        "selfGranted": false
      }
    }
  ]
}
```

