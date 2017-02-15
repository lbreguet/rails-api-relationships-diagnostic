# Rails API Relationships Diagnostic

Place your responses inside the fenced code-blocks where indivated by comments.

1.  Describe a reason why a join tables may be valuable.

  ```md
  Join tables are valuable because they allow us to link certain aspects of our
  data together.
  ```

1.  Provide a database table structure and explain the Entity Relationship that
  describes a many-to-many relationship for `Profiles`, `Movies` and `Favorites`
  (Think of Netflix). A `Profile` has a `given_name`, `surname` and `email` and a
  `Movies` have `title`, `release_date`, and `length` and `Favorites` would be the
  join table with references to `Movies` and `Profiles`.

  ```md
    Profile:
    -given_name
    -surname
    -email

    Movie:
    -title
    -release_date
    -length

    Favorites:
    -Movies
    -Profiles
  ```

1.  For the above example, what needs to be added to the Model files?

  ```rb
  class Profile < ActiveRecord::Base
    has_many :movies, through: :favorites
    has_many :favorites

    validates :given_name, presence: true
    validates :surname, presence: true
    validates :email, presence: true
  end
  ```

  ```rb
  class Movie < ActiveRecord::Base
    belongs_to :profile
    has_many :profiles, through: :favorites
    has_many :favorites

    validates :title, presence: true
    validates :release_date, presence: true
    validates :length, presence: true
  end
  ```

  ```rb
  class Favorite < ActiveRecord::Base
    belongs_to :profile
    belongs_to :movie

    validates :profile, presence: true
    validates :movie, presence: true
  end
  ```

1.  What is the purpose of a serializer? What would our `Profile` serializer look
like to show all movies favorited by a profile on
`http://localhost:3000/profiles/1`

  ```md
  A Serializer describes which attributes are to be set in the json format, what
  we are hoping to see as output.
  ```

  ```rb
  class ProfileSerializer < ActiveModel::Serializer
    attributes :id, :given_name, :surname, :email, :movie, :patients

    def patients
      object.patients.pluck(:id)
    end
  end
  ```

1.  What would the command be to _scaffold_ out a **join table** for Favorites from
the above `Movies` and `Profiles`.

  ```sh
  bin/rails generate scaffold favorites profile:references movie:references
  ```

1.  What is `Dependent: Destroy` and where/why would we use it?

  ```md
  `Dependent: Destroy` is a command that deletes/destroys the dependent
  resource, in this case the favorite. We would use for example if we wanted to
  delete a favorite.
  ```

1.  Think of **ANY** example where you would have a one-to-many relationship as well
as a many-to-many relationship in an application. You only need to list the
description about the resources and how they relate to one another.

  ```md
  Wikipedia article and a user. It would make sense to say that an article has
  many users and a user has many articles. But the computer sees a user has many
  articles and an article belongs to a user. So we would add another dependent
  resource called favorite_articles.
  ```
