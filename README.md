# scraping-allocine
scraping en php de allocine 


<?php
include "debut_pages_affichable.php";

#I do not put the password of my database
$COFilm = new PDO('mysql:', '', '');

$result="";
if(isset($_GET['search'])){
    $search = $_GET['search'];
    $rs_films = $COFilm->prepare('SELECT * FROM film WHERE film_titre LIKE :search');
    $rs_films->execute(array(':search'=> "%$search%"));
    while($film = $rs_films->fetch(PDO::FETCH_ASSOC))
    if (preg_match("~(^|[^a-z]){$search}($|[^a-z]|s])~i", $film['film_titre'])){
        $id = $film['film_id'];
        $titre = $film['film_titre'];
        $anneeproduction = $film['film_anneeproduction'];
        $affiche = $film['film_url_affiche'];
        $synopsis = $film['film_synopsis'];

        $rs_actors = $COFilm->prepare('SELECT * FROM 
        film_artist, artiste
        WHERE filmart_artiste_id = art_id
        AND filmart_film_id = :film_id
        AND filmart_type = "acteur"
        ORDER BY filmart_num_ordre ');
       $rs_actors->execute(array(':film_id' => $id));
        $liste_acteurs = 'Avec:';
        $premier = true;
        while ($actor = $rs_actors->fetch(PDO::FETCH_ASSOC)){
            $nom = $actor['art_nom'];
            $personnage = $actor ['art_role'];
            $liste_acteurs.= $premier?'':',';
            $liste_acteurs.= "$nom($personnage)";
            

            $premier = false;
        }
        $liste_acteurs.='.';

        
        $result .= "
        <div class=containerFilm>
            <img class=affiche src=$affiche>
            <div>
                <div class=titre>
                    <a href=https://www.allocine.fr/film/fichefilm_gen_cfilm=$id.html target=_blank>$titre</a>
                    <span class=annee>($anneeproduction)</span>
                </div>
                <div><b>$liste_acteurs</b></div>
                <div class=synopsys>$synopsis</div>
            </div>
        </div>";
}
}	

?>
<form method=???>
    <table>
      
        <tr><td><label for=search>Chercher : </label>
        <td><input name=search placeholder=mot_clé id=search autofocus
        value="<?php isset($_GET['search']) ? htmlspecialchars($_GET['search'], ENT_QUOTES) : ''; ?>">

        <tr><td></td><td><input type="submit" value="Chercher !"></td></tr>
        </table>
        </form>

<div id=listFilms>
    <?php echo $result; ?>
</div>


<?php include"fin_pages.php"; ?>

<style>
.containerFilm {
    display: flex;
    align-items: center;
    margin-bottom: 20px;
    height: 250px;
    background-color:grey;
    box-shadow: 2px 2px 2px 2px black; 
}

.containerFilm img {
    height: 100%;
    margin-right: 20px;
    transition: transform 0.3s ease;
}

.containerFilm .synopsis {
    margin-top: 10px;
}
.titre{
    color:black;
    font-size:30px;
    text-shadow: 1px 1px red ;
}


.containerFilm:hover img {
  transform: scale(1.1);
}
 




</style>
