---
layout: post
title: "TDD - Hard Wired Dependencies"
date: 2018-10-15
excerpt: "Test Driven Development - Supprimer les dépendences en dur"
tags: TDD Agile pratique code kata
feature: /images/2018-10-15-tdd-part-1/2018-10-15-tdd-part-1-affiche.png
comments: false
---

Je me suis inspiré de la présentation du coach __Patrick GIRY @PatrickGIRY__ lors d l'agile Testing night à la SG.

Dans ce cas pratique, je reprend le KATA __Trip Service__. Il s'agit de tester un code legacy avec des dépendances en dur (hard-wired dependencies).

Regardons la classe ```TripService```

```java
    public class TripService {

    	public List<Trip> getTripsByUser(User user) throws UserNotLoggedInException {
    		List<Trip> tripList = new ArrayList<Trip>();
    		User loggedUser = UserSession.getInstance().getLoggedUser();
    		boolean isFriend = false;
    		if (loggedUser != null) {
    			for (User friend : user.getFriends()) {
    				if (friend.equals(loggedUser)) {
    					isFriend = true;
    					break;
    				}
    			}
    			if (isFriend) {
    				tripList = TripDAO.findTripsByUser(user);
    			}
    			return tripList;
    		} else {
    			throw new UserNotLoggedInException();
    		}
    	}

    }
```

On constate 2 dépendences en dur :
* ```User loggedUser = UserSession.getInstance().getLoggedUser();```
* ```tripList = TripDAO.findTripsByUser(user);```

Afin d'avoir des TU fonctionnels, il faut casser ces dépendences.

```java
    public class TripServiceShould {

        private static final User UN_LOGGED_USER = null;

        private TripService tripService;

        @BeforeEach
        public void initService() {
            tripService = new TripService() ;
        }

        @Test
        public void throw_not_logged_exception_when_user_not_logged() {
            assertThatThrownBy(() -> tripService.getTripsByUser(UN_LOGGED_USER)).isInstanceOf(UserNotLoggedInException.class);
        }


    }
```

Le premier TU échoue :

```
    java.lang.AssertionError:
    Expecting:
      <net.dammak.kata.tripservice.exception.CollaboratorCallException: UserSession.getLoggedUser() should not be called in an unit test>
    to be an instance of:
      <net.dammak.kata.tripservice.exception.UserNotLoggedInException>
    but was:
      <"net.dammak.kata.tripservice.exception.CollaboratorCallException: UserSession.getLoggedUser() should not be called in an unit test
```

L'idée est d'extraire la récupération du user dans une autre méthode, en dehors du code métier :

```java
    public class TripService {

    	public List<Trip> getTripsByUser(User user) throws UserNotLoggedInException {
    		List<Trip> tripList = new ArrayList<Trip>();
        // Refactored Method
    		User loggedUser = getLoggedUser();
    		boolean isFriend = false;
        // Other code
    	}

    	protected User getLoggedUser() {
    		return UserSession.getInstance().getLoggedUser();
    	}

    }
```

Au niveau du test, il suffit de surcharger cette méthode, dans une classe Testable de service.

```java
    public class TripServiceShould {

        private static final User UN_LOGGED_USER = null;
        private static final User OTHER_USER = new User();

        private static class TestableTripService extends TripService {

            private User loggedUser;

            private TestableTripService(User user) {
                this.loggedUser = user ;
            }

            private static TestableTripService ofUnloggedUser() {
                return new TestableTripService(UN_LOGGED_USER) ;

            }

            @Override
            protected User getLoggedUser() {
                return loggedUser;
            }

        }

        @Test
        public void throw_not_logged_exception_when_user_not_logged() {
            assertThatThrownBy(() -> TestableTripService.ofUnloggedUser().getTripsByUser(OTHER_USER)).isInstanceOf(UserNotLoggedInException.class);
        }

    }
```

Le TU suivant échoue :
```java
    @Test
    void return_trip_to_tunis_and_trip_to_sfax_when_logged_user_friend_with_user_having_those_trips() {
        var userHavingFriend = new User() ;
        userHavingFriend.addTrip(TRIP_TO_SFAX);
        userHavingFriend.addTrip(TRIP_TO_TUNIS);
        userHavingFriend.addFriend(LOGGED_USER);
        List<Trip> trips = TestableTripService.ofLoggedUser().getTripsByUser(userHavingFriend);
        assertThat(trips).contains(TRIP_TO_TUNIS, TRIP_TO_SFAX);
    }
```

On applique la même méthode :

``` java
    public class TripService {

      public List<Trip> getTripsByUser(User user) throws UserNotLoggedInException {
        // some code
          if (isFriend) {
            tripList = findTripsByUser(user);
          }
          return tripList;
        } else {
          throw new UserNotLoggedInException();
        }
      }

      // Some code

      // Refactored Method
      protected User getLoggedUser() {
        return UserSession.getInstance().getLoggedUser();
      }

    }

    public class TripServiceShould {
      // Some code
      private static class TestableTripService extends TripService {

          // Some code

          @Override
          protected List<Trip> findTripsByUser(User user) {
              return user.trips() ;
          }

      }
    }
```
