# Rails API Relationships Diagnostic

Place your responses inside the fenced code-blocks where indivated by comments.

1.  Describe a reason why a join tables may be valuable.

```sh
A join table lets us report on data from two distinct entities that have a
many-to-many relationship.
```

1.  Provide a database table structure and explain the Entity Relationship that
describes a many-to-many relationship for `Profiles`, `Movies` and `Favorites`
(Think of Netflix). A `Profile` has a `given_name`, `surname` and `email` and a
`Movies` have `title`, `release_date`, and `length` and `Favorites` would be the
join table with references to `Movies` and `Profiles`.

```sh
A Profile has many Movies through Favorites. Profiles would be represented in a
table with the columns 'id' (serial primary key), 'given_name', 'surname' and
'email', all strings.

A Movie has many Profiles through Favorites. Movies would be represented in a
table with the columns 'id' (serial primary key), 'title' (string),
'release_date' (date) and 'length' (integer, or possibly some kind of
hours:minutes time format).

A Favorite has one Profile and has one Movie. Profiles would be represented in a
table with the columns 'profile_id' (references profiles[id]) and movie_id
(references movies[id]).
```

1.  For the above example, what needs to be added to the Model files?

```rb
class Profile < ActiveRecord::Base
  has_many movies, through: :favorites
end
```

```rb
class Movie < ActiveRecord::Base
  has_many profiles, through: favorites
end
```

```rb
class Favorite < ActiveRecord::Base
  has_one :profile, inverse_of :favorites
  has_one :movie, inverse_of :favorites
end
```

1.  What is the purpose of a serializer? What would our `Profile` serializer look
like to show all movies favorited by a profile on
`http://localhost:3000/profiles/1`

```sh
A serializer tells our API what information about a model can be sent back to
the client as part of a response.

Our 'Profile' serializer would look as follows:
```

```rb
class ProfileSerializer < ActiveModel::Serializer
  attributes :favorites
end
```

1.  What would the command be to _scaffold_ out a **join table** for Favorites from
the above `Movies` and `Profiles`.

```sh
bundle exec rails g scaffold Favorites movie:references profile:references
```

1.  What is `Dependent: Destroy` and where/why would we use it?

```sh
Dependent: Destroy is an option we an add to a relationship that is defined on
a model. It does two things when a request comes in to destroy an instance of
that model:

1. Allows that record to be destroyed, even if foreign key constraints from the specified relationship would prevent things
2. Destroys all instances of the related model, where that instance contain a reference to the relating model.

In our example, we would add 'dependent: destroy' to the Profile model, just
after 'has_many: favorites', so that when a user requests to destroy their
profile, all favorites belonging to that profile are also destroyed.
```


1.  Think of **ANY** example where you would have a one-to-many relationship as well
as a many-to-many relationship in an application. You only need to list the
description about the resources and how they relate to one another.

```sh
A bill-splitting app might include 4 resources: Users, Profiles, Bills, and Shares.

A User has one Profile, and a Profile belongs to a user.

A User has many Bills, through Shares. A Bill has many Users, through Shares.

A Share belongs to a User and belongs to a Bill.
```
