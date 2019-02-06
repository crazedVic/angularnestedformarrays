# Angular Reactive Forms with lots of Nesting

I looked everywhere for a solution I could just copy and paste into my project but with no luck, so here's my attempt at providing it for the next poor soul stuck in my situation. I've tried to keep the code as simple as possible.

# You have a league

```
league_details: this.fb.group({
        name: "",
        founder: ""
      })
```

# A league has teams

```
this.leagueForm = this.fb.group({
      league_details: this.fb.group({
        name: "",
        founder: ""
      }),
      teams: this.fb.array([this.teams])
    });
```

# Teams have names and players

```
get teams(): FormGroup {
    return this.fb.group({
      team_name: "",
      players: this.fb.array([this.players])
    });
  }
```

# Players have names and numbers

```
get players(): FormGroup {
    return this.fb.group({
      player_name: "",
      player_number: ""
    });
  }
```

# You have a FormGroup

```
<form [formGroup]="leagueForm">
```

# Show League Detail

```
<div class="form-header" formGroupName="league_details">
    <label>League Name <input formControlName="name"/></label>
    <label>Founder <input formControlName="founder"/></label>
  </div>
```

# Show Teams

It's VITAL you have a wrapping div that defines formArrayName!

```
<div formArrayName="teams">
    <div class="teams" *ngFor="let team of leagueForm.get('teams').controls;
             let teamIndex = index" [formGroupName]="teamIndex">
      <label>Team Name <input formControlName="team_name"/></label>
      <!--  div with formArrayName='players' goes here (see below) -->
    </div>
</div>
```

# Show Players inside Teams

Again, important you have a wrapping div with formArrayName. Also, you need to add players in context of the team. Notice in ngFor i am referencing the team variable
defined in the ngFor loop above for teams. I will also need to reference this team when removing or adding players.

Also notice the `<span>` element, this is how you reference the field for validation functionality.

```
<div formArrayName="players">
    <div class="players" *ngFor="let player of team.get('players').controls;
        let playerIndex=index" [formGroupName]="playerIndex">
        <label>Player Name <input formControlName="player_name" />
        <label>Player Number <input formControlName="player_number"/></label>
        <span *ngIf="player.get('player_number').touched">touched!</span>
    </div>
</div>
```

# Adding Teams

Place ADD button just above the div that defines the formArrayArrayName teams

```
 <button type="button" (click)="addTeam()">Add Team</button>
```

```
addTeam() {
    (this.leagueForm.get("teams") as FormArray).push(this.teams);
  }
```

# Adding Players

Place ADD button just above the div that defines the formArrayName players, and remember to pass in the team variable from the enclosing ngFor loop.

```
<button type="button" (click)="addPlayer(team)">Add Player</button>
```

```
addPlayer(team) {
    team.get("players").push(this.players);
  }
```
