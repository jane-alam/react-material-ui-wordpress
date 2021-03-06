## Changes from ORIGINAL Base - By File

### imports/ui/pages/index.js

* removed jumbotron import
* remove `<Jumbotron>` in favor of normal `<div>`
* `import RaisedButton from 'material-ui/RaisedButton'`
* use button for links to documentation
* add link to base material-ui documentation
* add a styles constant with some styles for things like the RasiedButtons

### imports/ui/startup/client/index.js

* `import injectTapEventPlugin from 'react-tap-event-plugin';` 
* add `injectTapEventPlugin();` see: http://www.material-ui.com/#/get-started/installation
* 

### imports/startup/client/routes.js

* `import MuiThemeProvider from 'material-ui/styles/MuiThemeProvider';`
* wrap router in material-ui theme with `<MuiThemeProvider></MuiThemeProvider>`

### imports/ui/components/loading.js

* exchange loading svg for from `<CircularProgress />` from http://www.material-ui.com/#/components/circular-progress
* 

### imports/ui/components/public-navigation.js

This had some larger changes, going from this:
```
import React from 'react';
import { LinkContainer } from 'react-router-bootstrap';
import { Nav, NavItem } from 'react-bootstrap';

export const PublicNavigation = () => (
  <Nav pullRight>
    <LinkContainer to="signup">
      <NavItem eventKey={ 1 } href="/signup">Sign Up</NavItem>
    </LinkContainer>
    <LinkContainer to="login">
      <NavItem eventKey={ 2 } href="/login">Log In</NavItem>
    </LinkContainer>
  </Nav>
);
```
to this:
```
import React from 'react';
import AppBar from 'material-ui/AppBar';
import FlatButton from 'material-ui/FlatButton';
import { Link } from 'react-router';

const styles = {
  navLink: {
    color: "#ffffff"
  }
}

export const PublicNavigation = () => (
	<div>
    	<Link to="/signup" >
    		<FlatButton style={styles.navLink} label="Signup" />
    	</Link>
    	<Link to="/login"  >
    		<FlatButton style={styles.navLink} label="Login" />
    	</Link>
    </div>
);
```

### imports/ui/components/authenticated-navigation.js

this had some big changes as well, going from this:
```
import React from 'react';
import { browserHistory } from 'react-router';
import { IndexLinkContainer, LinkContainer } from 'react-router-bootstrap';
import { Nav, NavItem, NavDropdown, MenuItem } from 'react-bootstrap';
import { Meteor } from 'meteor/meteor';

const handleLogout = () => Meteor.logout(() => browserHistory.push('/login'));

const userName = () => {
  const user = Meteor.user();
  const name = user && user.profile ? user.profile.name : '';
  return user ? `${name.first} ${name.last}` : '';
};

export const AuthenticatedNavigation = () => (
  <div>
    <Nav>
      <IndexLinkContainer to="/">
        <NavItem eventKey={ 1 } href="/">Index</NavItem>
      </IndexLinkContainer>
      <LinkContainer to="/documents">
        <NavItem eventKey={ 2 } href="/documents">Documents</NavItem>
      </LinkContainer>
    </Nav>
    <Nav pullRight>
      <NavDropdown eventKey={ 3 } title={ userName() } id="basic-nav-dropdown">
        <MenuItem eventKey={ 3.1 } onClick={ handleLogout }>Logout</MenuItem>
      </NavDropdown>
    </Nav>
  </div>
);

```


to this:

```
import React from 'react';
import { browserHistory, Link } from 'react-router';
import { Meteor } from 'meteor/meteor';
import AppBar from 'material-ui/AppBar';
import FlatButton from 'material-ui/FlatButton';
import Popover from 'material-ui/Popover';
import Menu from 'material-ui/Menu';
import MenuItem from 'material-ui/MenuItem';
import ArrowDropDown from 'material-ui/svg-icons/navigation/arrow-drop-down';



const userName = () => {
  const user = Meteor.user();
  const name = user && user.profile ? user.profile.name : '';
  return user ? `${name.first} ${name.last}` : '';
}

const styles = {
  navLink: {
    color: "#ffffff"
  }
}

export class AuthenticatedNavigation extends React.Component {

  constructor(props) {
    super(props);
    this.state = { 
      open: false
    };
    this.handleRequestClose = this.handleRequestClose.bind(this);
    this.handleLogout = this.handleLogout.bind(this);
    this.handleTouchTap = this.handleTouchTap.bind(this);
  }


  handleTouchTap(event){
    // This prevents ghost click.
    event.preventDefault();

    this.setState({
      open: true,
      anchorEl: event.currentTarget,
    });
  }

handleLogout() {
  Meteor.logout(() => browserHistory.push('/login'));
}

  handleRequestClose(){
    this.setState({
      open: false,
    });
  }

  render() {
    return <div>
            <Link to="/"  ><FlatButton style={styles.navLink} label="Index" /></Link>
            <Link to="/documents" ><FlatButton style={styles.navLink} label="Documents" /></Link>
            <FlatButton label="UserName" labelPosition="before" style={styles.navLink}  icon={<ArrowDropDown />} onTouchTap={this.handleTouchTap} />     
            <Popover
              open={this.state.open}
              anchorEl={this.state.anchorEl}
              anchorOrigin={{horizontal: 'left', vertical: 'bottom'}}
              targetOrigin={{horizontal: 'left', vertical: 'top'}}
              onRequestClose={this.handleRequestClose}
            >
              <Menu>
                <MenuItem primaryText="Logout" onTouchTap={ this.handleLogout }  />
              </Menu>
            </Popover>
        </div>
  }
  
}
```

The main driver of the increased LOC was the dropdown needed more code and state for the popover to work. There are probably better options but this does the trick for now.
